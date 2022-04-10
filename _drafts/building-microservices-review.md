---
title: Building Microservices book review
excerpt: 'review of the book written by Sam Newman'
categories:
  - blog
tags:
  - microservices
  - book review
header:
  image: '/images/header.jpg'
---

# Intro

# Review

Los capítulos son muy interesantes y cubren todo el espectro de desarrollo de microservicios, tanto la parte puramente técnica, como también la cultural sobre una organización que quiere adoptar este approach.

El libro es muy teórico. El caso de negocio que se muestra como ejemplo no tiene mucho protagonismo. No obstante, el nivel de teoría seguido de los pro tips que da el autor, son realmente útiles en muchos casos y hace que cierto capítulos se transformen en obras maestras (por ejemplo, el capítulo 4, de integración, la seccion que explica como manejar breaking changes es oro puro, una verdadera obra de arte)

# Chapter 01

# Chapter 02 - The evolutionary architect

Architects have an important role. They have to assure that the technical effort of the team is alligned with the technical vision. Because of that, it is important to define the Strategy, Principles and Practices.

Strategy: `TODO TODO TODO TODO`

Principles: `TODO TODO TODO TODO`

Practices: `TODO TODO TODO TODO`

Architects use to work on a higher level, but they don't have to leave coding at all. It is important that they continue doing that even through pair programming from time to time.

Another important subject is that an architect should guide the team towards the tech vision accomplishment, but it doesn't mean it had to be a one way communication. It should communicate and participate together with the team. He can agree or not with some tech ideas provided by the team. Sometimes it is a tradeoff, when you, as an architect, had to agreed in some minor aspects to empower the rest of the team.

# Chapter 03 - How to model services

bounded context permite agrupar objetos/modelos dentro de una misma boundary, y compartir modelos en comun a través de shared modules.

permiten bajo acoplamiento entre servicios, y alta cohesión dentro de los límites de un context bound.

# Chapter 04 - Integration

Existen diferentes formas de comunicar microservicios entre sí (SOAP, XML, REST, gRPC). No obstante, es clave tener siempre en mente los siguientes principios:

- evitar breaking changes
- que las APIs sean technology agnostic
- que los servicios y la forma de integrarse sean simples para los consumers: esto puede involucrar, por ejemplo, crear client libraries. No obstante, es importante en este caso tener en cuenta el acoplamiento (con client libraries es más facil acoplarse con el servicio, y por lo tanto generar breaking changes)
- ocultar detalles de implementación

Formas de integración:

- shared database: diferentes servicios le pegan a la misma db. Fuerte acoplamiento, quita flexbilidad (dificil poder modificar estructuras, dado que el impacto puede propagarse a varios servicios)

Synchronous vs Asynchronous

Orchestration vs Choreogrphyc

- con orquestación es facil que el orquestador se vuelva un ms god (esto es un antipatron), y que el resto de los servicios sean simples CRUD

- choreographyc está mas basado en eventos. los microservicios se mantienen desacoplados. Como desventaja, puede decirse que el flujo de negocio no es facil de ver a primera vista. Esto implica, que se requiere más esfuerzo de monitoring y tracking

Rest

- Rest sobre HTTP brinda muchas facilidades y una semantica clara. A su vez, al correr sobre HTTP se tienen mucho tooling para diferentes propositos (caching, analisis de trafico, monitoreo, seguridad).

Event Driven/ Sync vs Async communication

Usar comunicacion asincrónica y brokers de mensajería trae también ciertas complejidades, como los mecanimos de fallback ante fallos de procesamiento de mensajes. Recomendaciones:

- configurar retries minimos y maximos en los consumers
- tener una dead letter queue para el tratamiento de mensajes fallidos
- tener una tool UI de visualizacion de mensajes

Siempre se recomienda empezar simple, tener un buen monitoring y uso de `Correlation ID` como una forma de tener tracking de requests que viajan por varios servicios. Si teniendo bien las bases, notamos que la comunicación sincronica trae problemas de performance que afectan al negocio, recién ahí considerar flujos asíncronicos/mensajeria/event-driven.

DRY

Se habla mucho del principio DRY. Tener código reutilizable hace facil el razonamiento y reduce el volumen de código, no obstante, a veces puede generar un fuerte acoplamiento entre partes. Por ejemplo, si creamos una `shared library` que contiene todos los objetos de nuestro dominio para ser usada por varios de nuestros servicios, estamos generando un fuerte acoplamiento. Un cambio en ella, puede afectar negativamente a todos los dependientes.

`quote`: Don't violate DRY inside the boundaries of a microservices, but don't worry on violating it across microservices.

Access by Reference

Algunas veces, cuando pasamos info asincrónicamente entre microservicios (más que nada, la info de dominio que viaja de un servicio a otro) puede ocurrir que cuando el servicio consumer lee dicha data (por ejemplo: customer info), dicha info esté desactualizada. Una recomendación, es pasar una referencia al recurso (ej: una uri customers/:id), y que el servicio consumer se comunique con el servicio holder de esa info para obtener la info actualizada. No obstante, estamos agregando más carga al servicio inicial, pero es una posible estrategia cuando tenemos escenarios de este tipo.

Versioning

- dilatar los breacking changes tanto como se pueda
- siempre tener en cuenta la tecnología de integración que vamos a usar
- tratar de atajar los breaking changes cuanto antes. Sugerencias acá: hacer tests de las librerias client contra el servicio updateado, usar `Consumer-driven contracts`.
- usar semantic versioning
- tratar de evitar que tanto el cliente como el servidor de lockeen ante un upgrade del servidor que contenga breaking changes. Una alternativa es que el servidor mantenga tanto la api vieja como la nueva (ej: usando versioning en la url, en el caso de rest, `/v1`, `/v2`)

Semantic Versioning

Es una forma de saber si una nueva versión puede o no afectar negativamente a los clients.

MAJOR.MINOR.PATCH

- MAJOR: cuando se actualiza, indica que va a haber breaking changes (no backward compatibility)

- MINOR: cuando se actualiza, indica que se han añadido nuevos features que son backgward compatible => entonces el cliente no ve camvbios en el comportamiento (todo esta OK, en todo caso, hay nuevas funcionalidades)

- PATCH: cuando se actualiza, indica que se han hecho bug fixes de funcionalidad existente.

Coexists Different Endpoints

tratar de evitar que tanto el cliente como el servidor de lockeen ante un upgrade del servidor que contenga breaking changes. Una alternativa es que el servidor mantenga tanto la api vieja como la nueva (ej: usando versioning en la url, en el caso de rest, `/v1`, `/v2`)

Esto más da tiempo al cliente de upgradearse y no bloquea el ciclo de release del servidor. Una vez el cliente se integre a la nueva api, la api anterior puede deprecarse.

No obstante, este approach tiene downsides en cuanto a tests. Porque puede darse el caso de que varios clientes no se actualicen, y tener varias versiones de la api (ej: v1, v2 y v3). las cuales hay que seguir mantiendo respecto a tests. Una estrategia es, internamente, en las apis mas viejas adaptar los payloads y que hagan requests a la api más actual. Eso hace más facil el testing (solo debemos testear la api más actual) y también poder deprecar dichas apis.

## TODO

- investigar [mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) (for rest)
- investigar [Varnish](https://varnish-cache.org/) (for rest)
- [rest maturity model](https://martinfowler.com/articles/richardsonMaturityModel.html)
- [Tolerante Reader pattern](https://martinfowler.com/bliki/TolerantReader.html)
- Consumer-driven contracts
- blue/green deployments
- canary releases

# Conclusion