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