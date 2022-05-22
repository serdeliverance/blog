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

# The audience of this book

I think this book is a must read for any software developer, tech lead and architect involved in the delivery of value through the creation of software. It does not matter if you are working on a monolith or using microservices, this book gives you lot of insights based on the broad and real world experience the author has on this field.

# The Author

`TODO`

# Review

`TODO`

# Conclusion

# ----------------------------------------
# Notas mias
# ----------------------------------------

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

## Chapter 05 - Splitting the monolith

Approaches para separar el monolito

### Identificar Seams

- identificar bounded contexts y separarlos dentro de `packages` en el mismo proyecto. Apoyarse en las herramientas de refactor que nos provea el IDE.

- apoyarse en tests para este propósito

`TIP`: puede ser útil tener una herramienta de análisis de dependencias que provea una visualización (estilo grafo) de las mismas.

### Reasons for splitting the monolith

Es importante saber el por qué es necesario hacerlo. Dichas razones pueden ser:

- el arribo de una gran cantidad de features que se sabe van a impactar sobre un mismo bounded context (entonces conviene tenerlo como ms)
- team structure. hay que pensar en el equipo. Supongamos que tenemos un equipo distribuido en londres y en el salvador, tal vez nos convenga tener bounded contexts separados para que cada equipo trabaje con un set especifico de features y tenga un mejor ownership (esto es importante tambien por cuestiones de huso horario)
- technology: pueden haber razones para que un bounded context sea implementado con una tecno especifica, entonces este sea un buen driver para extraerlo del monolito.
- tangled dependencies: qué tan atadas entre sí están las dependencias. esto puede hacer al código mucho menos mantenible.

Getting to grips with the problem

Generalmente la base de datos es donde se genera el acoplamiento (dado que es el mecanismo de integración más común). Entonces, una estrategia para empezar a visualizar los bounded context es splittear el repository layer

![repository layer splitted]({{ site.baseurl }}/assets/images/building-microservices-review/repository_layer_splitted.png "Repository Layer Splitted")

Esto es un gran avance, pero no es suficiente. También puede ser útil analizar las dependencias entre tablas a nivel base de datos, por ejemplo, usando [SchemaSpy](https://schemaspy.org/)

Casos puntuales

- breaking foreign key relationships: cuando un bounded context referencia a tablas de otro bounded context, es mejor eliminar esta referencia (integración via db), y hacer que el servicio referenciado exponga una API para que el segundo la consuma

- shared static data: cuando tenemos info estática que es compartida entre varios servicios (ej: `country codes`), es mejor extraer dicho código comun en archivos de configuración (dado que extraer esto en un servicio aparte es un overkill)

- shared data: cuando vemos que varios servicios le pegan a una misma tabla, y podemos reconocer que esta tabla o conjunto de tablas representa un bounded context en sí misma, entonces podemos crear un ms que la encapsule y exponer API endpoints para que sea accedida.

Staging the Break

O en otras palabras: "splittear de a poco". La recomendación del autor es qué, una vez que identificamos los "pliegues" (dónde cortar, donde se delimitan los bounded contexts) a nivel servicio y a nivel schema de datos, lo que `nos conviene hacer es primero splittear el schema de db. Luego, splitear en dos servicios`.

Splittear primero a nivel datos nos permite poder medir el impacto de dicha separación. Más que nada, esto se refleja en los `joins` que hay que hacer en memoria cuando unimos data de dos squemas separados. También nos permite ver si tenemos algún issue con la transaccionalidad (es decir, si teniamos transaccionalidad con data que ahora está en schemas separados). Si todo eso está ok, nos da una base sólida para poder movernos hacia un split de servicios.

![staging the break]({{ site.baseurl }}/assets/images/building-microservices-review/stagging_service_separation.png "Staging the Break")

Transaccionalidad

Cuando spliteamos servicios, perdemos algo que sí teniamos en el monolito out of the box: `integridad transaccionalidad`. Ahora, ya no tenemos un `transaccional boundary` que podríamos gestionar facilmente con transacciones de DB, sino que tenemos boundaries entre servicios. Estrategias para esto:

- implementar retries: cuando implementamos retries, asumimos que ciertas operaciones fallidas se reintentaran luego. esto introduce el concepto de `eventual consistency`. Es decir, asentúa la idea de que el sistema será consistente en un punto del futuro.
- alguna forma de orquestación, para orquestar tanto flujos de success como de failure (sería una rollback a mano que involucra potencialmente a varios microservicios)

Tips

- al ver un flujo de negocio, siempre preguntarse si tiene sentido que sea transaccional o si podría usarse `eventual consistency`.

- si si o si se requiere que un flujo de negocio sea transaccional, tratar de evitar splitear los servicios involucrados, para poder gestionarlo de una forma similar al monolito.

Reporting

otro `TIP`: `siempre tener en cuenta el reporting`. Ahora que estamos spliteando microservicios, obtener reportes no estan facil como antes (donde con un par de joins estabas hecho).

Hay diferentes approaches para poder seguir generando reporting:

- reporting database: consiste en tener una db replica. entonces el schema del servicio va replicando contra esta database. Acá tendríamos todo, sin embargo podemos tener problemas como el fuerte acoplamiento entre ambos esquemas (es decir, si cambia la db del servicio, podemos romper la db de replica). La segunda ventaja es que, como replicamos la base tal cual, estamos atados a la db original, la cual tal vez no sea la más performante para el reporting que necesitamos... es decir, perdemos la libertad de realizar optimizaciones sobre nuestra db de reporting porque su infraestructura es una replica de la db de la que se nutre. También perdemos la posibilidad de elegir una db que se adapte mejor a las necesidades del reporting.

# Posibles soluciones:

## data retrieval via `Service Calls`

que los servicios expongan endpoints donde se pueda consultar info que luego el service de reporte utilizara. Esto tiene un riesgo cuando el volumen de datos que se va a consultar es alto. Por ej: si consultamos ventas de hace 2 años para hacer un reporte

Posibles mejoras:

- que el servicio exponga bach API endopoints: es decir, endpoints a los que se les pueda consultar por un conjunto de IDs
- ofrecer endpoints con opciones de paginado de resultados

## Testing

se hace fuerte énfasis en las complicaciones que traen los tests `e2e` y se explica por qué es mejor tener una limitada cantidad de ellos (más cuando dichos tests involucran a servicios mantenidos por varios equipos).

Se habla de los `smoke tests` como una manera de testear rápidamente que un servicio, al ser deployado en un ambiente productivo, no tenga problemas de configuración.

Se introduce `Consumer-Driven Contracts` como una forma de testear integraciones, a través de contratos que pueden testearse durante el pipeline de `CI/CD` y que son menos costosos que los `e2e`.

Como complemento, se nombra las técnicas `blue/green` y `canary deployment` como otras formas de testear una aplicación.

Es importante también ver al testing como una herramienta para obtener rápido feedback, y tratar de tener un balance entre el testing y la rapidez con la que resolvemos issues en producción (porque de nada nos sirve tener muchos tests, si cuando sale un issue en prod no sabemos como resolverlo)

A lo largo de todo el capítulo se remarca la importancia de tener un buen `monitoring`.

# Monitoring

si es un monolito, se pueden analizar los logs con un simple `grep`. En cambio, si tenemos algunos hosts mas, se puede usar multiplexacion ssh. Si, sin embargo, llegamos a tener ya un conjunto importante de ms, nos conviene empezar a utilizar herramientas como `logtash` y `kibana` para el procesamiento y visualización de logs. También se recomeinda `Graphite`.

Es importante tener en cuenta patrones/mecanismos como el `Correlational ID` para el tracking de requests que involucran varios ms.

Nagios es una buena herramienta de monitoreo.

# Security

Es importante entender la naturaleza de la información que vamos a disponer, el medio y el cliente al cuál va dirigida.

Existen diferentes mecanismo de seguridad: HTTP basic authentication, HTTPS, client certificates, API keys, network hardening, data encryption, OS patching, etc.

Es importante gestionar la seguridad en diferentes niveles y tener en cuenta quien es el cliente. Un ejemplo de esto es el citado en el libro: `Music Corp`. Aquí se muestra que un sistema puede tener diferentes clientes: web, api client o un cliente que se comunica directamente por un canal securizado. Las diferentes formas de comunicacion puede sugerir distintos mecanismos de seguridad. Ej: para un web client, usar HTTPS para info que requiera login es una buena opción, y utilizar HTTP (no secure) por info que no requiere medidas de seguridad es una buena opción (además, permite tener un buen caching). Otro ejemplo es el de un third party provider que se comunica con nuestra API a partir de un medio seguro. Al ser un único (o una cantidad muy acotada de clientes), con clients certificates se pueden aplicar medidas de seguridad.

El autor menciona que es importante que la seguridad se relacione con la estructura organizacional, y evitar hilar muy fino con la granularidad de los roles y permisos, por ejemplo. También se menciona la importancia del otorgamiento y revocación de permisos.

# Conway's Law

`if the organization building the system is more loosely coupled, the systems being built tend toward the more modular, and therefore hopefully less coupled`

`El diseño de sistemas refleja la estructura comunicacional de la empresa que lo diseña` (parafraseando)

Si tenemos un equipo estilo `two-pizza teams`, veremos que su impacto y frecuencia de delivery son altos, ya que la comunicación es ágil. Si, por el contrario, tenemos un equipo distribuido geográficamente, que trabaja en zonas horarias muy dispares, dicha estructura se reflejará en la frecuencia de cambios (por ejemplo, consensuar sobre cambios se vuelve una tarea ardua) y en el diseño.

# Microservices at Scale

# Bringing all things together

Principios de los microservicios:

- modeled around business concepts: usar bounded-context para modelar microservicios en función del dominio. Esto hace que sea más facil distinguir los limites de cada uno y representar los procesos de negocio.

- culture of automation: automated testing, deploy the same way everywhere, continuous delivery... otras recomendacions: custom images (para aumentar velocidad del deployment)

- hide internal implementation details: no importa que alla detras un ms, es mejor que esté oculto para el resto de los ms, así se evita el acoplamiento (por ejemplo: un ms no debería saber/conocer la db de otro, es un detalle de implementación). Usar protocolos que sean tecnology agnostic para facilitar la integracion.

- isolate failure: debemos considerar siempre el fallo. estamos usando ms, asi que tenemos que ser conscientes de los escenarios, para evitar los fallos en cascada. Recomendaciones: timeouts, bulkheads, circuit breakers, y siempre pensar en el impacto que tiene para el cliente.

- deploy independently: recomendaciones. `one server per host`, para evitar que el deploy de un ms afecte a otros. `blue/green deployment` para separar deploy de release, reduciendo la posibilidad de que un release salga mal. `consumer-driven contracts` para catchear breaking changes antes de que ocurran. Siempre tener la `siguiente norma`: `el deploy de un ms no debe implicar que otros ms deban ser deployados tambien`, ya que esto (tener varias versiones atadas...) genera un fuerte acoplamiento y aumenta el riesgo de que algo salga mal.

- decentralize all the things: mas orientado al `self-service` e independencia de los equipos, para poder testear y realizar releases de manera autónoma. se recomienda `choreography` sobre `orchestration`, ya que esto evita que la lógica de negocio se concentre en un solo lugar/equipo. Tener en cuenta la `Conway's Law` y acomodar los equipos de acuerdo a la estructura organizacional.

- highly observable

Algunas palabras sobre ms y recoemndaciones generales:

Siempre pensar en el dominio. Definir bien los `bounded contexts`, esto va a permitir tener una buena separacion entre servicios y tener una armoniosa `service to service communication`. Entener el dominio es clave.

Empezar un `Greenfield` project es desafiante. Se recomienda en este caso, arrancar con un monolito, y a medida que el sistema crezca y asi también nuestro conocimiento del dominio, recién ahí considerar splittear bounded contexts. Obviamente, empezar a splitear cuando tengamos un sistema estable.

Pensar en una `evolutionary architecture`, realizar cambios incrementales. Reducir en scope las desiciones, para que el impacto en caso de fallo sea menor. Buscar siempre un enfoque incremental

## TODO

- investigar [mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) (for rest)
- investigar [Varnish](https://varnish-cache.org/) (for rest)
- [rest maturity model](https://martinfowler.com/articles/richardsonMaturityModel.html)
- [Tolerante Reader pattern](https://martinfowler.com/bliki/TolerantReader.html)
- Consumer-driven contracts
- blue/green deployments
- canary releases
- [Strangler Pattern](https://martinfowler.com/bliki/StranglerFigApplication.html) by `Martin Fowler`
- [Strangler Pattern](https://microservices.io/patterns/refactoring/strangler-application.html) by `Chris Richardson`

# Review

Es un libro sobre `thoughts about microservices`. El auto es una persona con dos décadas de experiencia y un gran entusiasta de este estilo arquitectural. Debido a esto, el libro está lleno de `tips` basados en experiencia sobre proyectos reales. 

# Conclusion