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

# Conclusion