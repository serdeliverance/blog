---
title: Parallel Streams in Java
excerpt: ''
categories:
  - blog
tags:
  - java
header:
  image: '/images/header.jpg'
---

# Intro

# ParallelStream

Para convertir una collection en un `parallelStream` hay que invocar al metodo del mismo nombre. Ejemplo:

``` java
public long parallelSum(long n) {
    return Stream.iterate(1L, i -> i + 1)
            .limit(n)
            .parallel()
            .reduce(0L, Long::sum);
}
```

Un `parallelStream` es un `stream` que divide sus elementos en `chunks`, y cada `chunk` se asigna a un diferente `thread` para su posterior procesamiento.

Esto permite poder dividir nuestro workload en chunks y aprovechar el multicore de nuestro sistema.

`parallelStream` utiliza mucho el `fork/join` framework, lo cual permite poder sincronizar `tasks` de una forma menos propensa a errores

- `parallel` usa el `fork/join` `thread pool` que, por defecto, es un pool de threads que tiene tantos hilos como cores tenga nuestro procesador (`Runtime.getRuntime().availableProcessors()`)

No obstante, convertir a una collection en `parallel` no siempre trae beneficios de performance. Hay que saber muy bien cual es la naturaleza del pipeline que estamos definiendo. Hay operaciones que son mas paralelizables que otras. Si no tenemos esto en cuenta, usar `parallel` va a terminar siendo contraproducente en cuanto a performance.

Por ejemplo, en el caso anterior, si usamos parallel, dado que nuestro stream usa `iterate` (que es una funcion recursiva, que va calculando y generando elementos teniendo en cuenta el resultado de la iteracion anterior), no vamos a poder tener el conjunto de elementos a `splitear` (es decir, no vamos a tener los `chunks`), porque se debe terminar de procesar el iterate antes de eso, lo cual no nos va a poder permitir paralelizar como nosotros queremos.

# Fork/Join framework

Es a framework para el procesamiento de tareas en paralelo. Es una forma de implementar el patrón `divide and conquer`. Consiste en dividir una tarea recursivamente hasta que ya no pueda dividirse más, una vez llegado a este punto se procesa en paralelo todas esas subtasks y luego se mergea (`join`) de los resultados.

`TODO imagen fork-join`

`Fork Join` es una implementación del `ExecutorService`. Las tareas de ejecutan en el llamado `ForkJoinPool`. Este thread pool es uno que contiene tantos threads como cantidad de `CPUs` disponibles tenga el sistema donde corra la `JVM`.

El tipo de tarea que submitieamos al pool tiene que ser de tipo `RecursiveTask<R>`, siendo `R` el tipo de resultado de la computación final. Para poder crear este tipo de task debemos crear una subclase de dicha interfaz e implementar el método `compute()`.

``` java
protected abstract R compute();
```

El algoritmo de este método es simple:

```
if (task is small enough or no longer divisible) {
  compute task sequentially
} else {
  split task in two subtasks
  call this methods recursively possibly further splitting each subtask
  wait for the completion of all subtasks
  combine the results of each subtask
}
```

El siguiente es un ejemplo de código de la implementación de una `RecursiveTask`.

``` java
public class ForkJoinSumCalculator extends RecursiveTask<Long> {

    private final long[] numbers;
    private final int start;
    private final int end;

    public static final long THRESHOLD = 10_000;

    public ForkJoinSumCalculator(long[] numbers) {
        this(numbers, 0, numbers.length);
    }

    private ForkJoinSumCalculator(long[] numbers, int start, int end) {
        this.numbers = numbers;
        this.start = start;
        this.end = end;
    }

    @Override
    protected Long compute() {
        int length = end - start;
        if (length <= THRESHOLD) {
            return computeSequentially();
        }
        ForkJoinSumCalculator leftTask =
                new ForkJoinSumCalculator(numbers, start, start + length/2);
        leftTask.fork();
        ForkJoinSumCalculator rightTask =
                new ForkJoinSumCalculator(numbers, start + length/2, end);
        Long rightResult = rightTask.compute();
        Long leftResult = leftTask.join();
        return leftResult + rightResult;
    }

    private long computeSequentially() {
        long sum = 0;
        for (int i = start; i < end; i++) {
            sum += numbers[i];
        }
        return sum;
    }
}
```

Si queres invocar la tarea, dicha task se debe proveer a un `ForkJoinPool`.

``` java
public static long forkJoinSum(long n) {
    long[] numbers = LongStream.rangeClosed(1, n).toArray();
    ForkJoinTask<Long> task = new ForkJoinSumCalculator(numbers);
    return new ForkJoinPool().invoke(task);
}
```

En este caso se está instanciando un `ForkJoinPool`. Esto en la práctica no es una buena idea. Se tendría que tener una referencia a uno, y dicha instancia debería ser un singleton en toda la aplicación.