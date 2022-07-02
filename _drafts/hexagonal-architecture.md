---
title: Hexagonal Architecture
excerpt: 'TODO TODO TODO TODO TODO'
categories:
  - blog
tags:
  - architecture
header:
  image: '/images/header.jpg'
---

# Intro

# Layered architecture

`TODO brief explanation`

`TODO a brief diagram`

# Disadvantages or Layered architecture

- la capa de persistencia tiene más importancia (`data driven`). Esto se refleja en que el desarrollo siempre empieza por dicha capa y luego se pasa a las siguientes. Con los ORM y frameworks de la capa de acceso a datos, todavía toma mas predominancia y se genera un fuerte acoplamiento.
- al desarrollar un sistema, generamos un modelo que representa el negocio y que expone funcionalidades a los usuarios. Perdemos esta capacidad dado que estamos dando más importancia al `estado` (como se ha mencionado en el punto anterior, por enfatizar la capa de persistencia), que al `comportamiento` (es decir, al dominio). El dominio pierde protagonismo y, lo que es peor, el dominio depende fuertemente/está fuertemente acoplado a la capa de persistencia.
- es facil tomar `shortcuts`, y empezar a mixear código de diferentes capas (por ejemplo, hacer que la capa web referencie directamente a la capa de persistencia). Se requiere disciplina en el equipo para evitar las `Broken Windows`.
- es dificil paralelizar (debido a que el desarrollo empieza desde la capa de persistencia y, hasta no tenerlo hecho, no se puede avanzar hacia las capas mas externas)
- es dificil identificar los `use case` en el código, dado que tenemos `fat services` (ej: `UserService`, por si a alguno le suena), es decir servicios que involucran una rebanada de varios casos de uso. Sumado a eso, gran parte del código se encuentra en la persistencia, y eso si no empezamos a mixear capas.

En aplicaciones `CRUD` puede ser una buena opción. No obstante, en aplicaciones que tengan muchas reglas de negocio y que tengan proyección de crecimiento, una arquitectura de capas tradicional no es la mejor opción para generar código mantenible y fácilmente adaptable a cambios.

# Some SOLID principles to the rescue

Para migrar a una arquitectura/organización de componentes más mantenible, podemos basarnos en dos sólidos principios de la `OOP`, o más precisamente, de `SOLID`:

- `Single Responsibility Principle`
- `Dependency Inversion`

# Single Resposibility Principle

El principio de `Single Responsibility` generalmente es interpretado como que un componente debe hacer una sola cosa y hacerla bien. No obstante, podría interpretarse como que `un componente debe tener un solo motivo para cambiar`. En la práctica, en sistemas muy mal arquitecturados, generalmente no vemos esto, y una modificación en un componente, generalmente desencadena cambio en quienes dependen de él. No obstante, si logramos conservar este principio, podemos hacer que los componentes sean más mantenibles, y que el impacto en componentes adyacentes no les afecte, o que dicho impacto sea mínimo. Esto, extrapolado a nivel arquitectura, nos permitiría tener componentes desacoplados y con una funcionalidad o scope bien definidos.

# Dependency Inversion Principle

> We can turn around (invert) the direction of any dependency with our codebase.
>
> -- <cite>Misterious Head of Wisdom</cite>

Inversion de dependencias es una tecnica clave en el camino hacia un dominio completamente desacoplado e independiente de adapter e integraciones. Recordemos que en arquitectura hexagonal, el flujo de dependencias es de afuera hacia adentro, siendo las capas internas o core, un conjunto de elementos completamente desacoplado que define las piezas fundamentales del dominio y las reglas de negocio. Para poder llegar a esto, es necesario tener un mecanismo que nos ayude a "invertir" el sentido de las dependencias, esto nos ayuda a que el dominio solo tenga una unica razon de cambiar (Single Responsibility Principle), desacoplandolo de los adapters y del resto del mundo.

Veamos un ejemplo

`TODO ejemplo con diagrmas`

# Hexagonal Architecture

`TODO `

Los beneficios de la arquitectura hexagonal son, basicamente:

- tener una clara separacion de responsabilidades, donde el dominio pueda evolucionar independientemente de las capas externas
- facilitar la identificacion de componentes con una estructura expresiva. tener componentes especificos donde su responsabilidad es clara y su scope acotado
- poder paralelizar el trabajo (diferentes devs trabajando en distintos use cases sin overlaping)
- poder refactorizar agregando nuevos adapters e integraciones con nulo impacto en las capas core del negocio
- poder testear/validar las reglas de negocio independientemente del mundo interior

# Packing

Es crucial tener un modelo de packaging que sea fiel a los principios de arquitectura hexagonal. Al principio puede parecer dificil, pero una vez asimilados, nos va a dar grandes beneficios. Se busca que el packaging sea muy expresivo, para que, como se menciono anteriormente, sea facil identificar no solo los casos de uso, sino también los adapters. Esto ayuda mucho para saber dónde hay que trabajar o hacer foco al momento de desarrollar nuevos features (crear, modificar adapters, o implementar nuevos use cases).

Este packaging es muy expresivo y hace la arquitectura más explicita para los developers.

Este packagin esta explicado en java, pero dicha estructura puede trasladarse a otros lenguajes.

# Conclusion

`TODO intro a la seccion y resumen`

En proximos posts estaremos viendo dos series dedicadas a la implementacion de esta arquitectura: una refactorizando una aplicacion tradicional java/spring boot, y otra creando un nuevo desde cero en Scala 3 con pure FP.

# --------------------
# Notas
# --------------------

domain => contains the domain model
application => contains service layer around the domain model
ej: SendMoneyService implementa el port in interface SendMoneyUseCase

```
├── application
│   ├── SendMoneyService
│   ├── ports
│   │   ├── in
|   |   |    ├── SendMoneyUseCase
│   │   └── out
|   |        ├── LoadAccountPort    // lo implementa adapter.out.persistence.ARepository
```

adapter => contains incoming adapters that calls application layer incoming ports
        => contains also outgoing adapters that provides implementation for the application layer outgoing ports