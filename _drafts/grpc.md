---
title: gRPC getting started
date: 2021-11-06 20:20:52
excerpt: "An introduction to gRPC with an example using Play Framework"
categories:
  - blog
tags:
  - akka
  - scala
header:
  image: "/images/header.jpg"
---

# Intro

# REST + JSON

# The problem

- JSON consume mucho ancho de banda. Dado que es un formato horientado a texto.

- Otro problema con JSON es el mantenimiento evolutivo. Cuando los payloads cambian, tanto cliente como servidor deben adaptarse. Muchas generando duplicación de código

# gRPC

gRPC trata de optimizar las siguientes 3 capas del modelo OSI:

- Application (HTTP2)
- Presentation (Protobuf)
- Session (RPC)

Ventajas:

- más eficiente a nivel recursos. Esto debido a protocol buffers + HTTP2. Protocol buffers permite que se transfieran mensajes mas compactos sobre la red. HTTP2 permite multiplexar muchos mensajes en una sola conexión TCP

- streaming/full duplex

- formal contract: los contratos son más fuertes.

- no es necesario codear clientes y servidores, protocol buffers nos genera el código necesario para comunicar cliente con servidor.

- multiplataforma: a partir del mismo contrato, se puede generar código cliente y servidor para múltiples plataforma.

# Protocol buffers

Es un estandard para serializar data, y tiene como objetivo minimizar la cantidad de bytes necesarios para representar una estructura.

Protocol buffers nos ofrece su propia sintaxis para describir tipos/mensajes, y también tiene un IDL (Interface Definition Language), que permite autogenerar código cliente/servidor para diferentes lenguajes. Este código se encarga de serializar/deserializar los mensajes que envian/reciben nuestros clientes/servidores gRPC.

Describimos nuestros mensajes y métodos en archivos con extensión `.proto`.

`TODO ejemplo`

Viendo el ejemplo, se puede notar que cada mensaje está fuertemente tipado. Sumado a eso, cada atributo tiene un `tag` (el número de la asignación), el cuál sirve para indicar el orden en que se van a serializar los atributos.

Para ver la lista de los tipos disponibles, mejor remitirse a la [documentación oficial](https://developers.google.com/protocol-buffers/docs/overview)

Importantes caracteristas a mencionar de los protofiles. Se pueden definir: nested types, maps, oneOf y también enums.

A los métodos se los conoce como `service` o `service interface` en la jerga de `gRPC`. Obviamente, estos serían las RPCs. Hay diferentes tipos de servicios: unary, server streaming, client streaming o bidirectional streaming.

`TODO explicar los diferentes tipos`

Protocol buffers tiene dos componentes fundamentales:

- `compiler`
- `runtime`

# Http 2

# Example

# Error handling

# Versioning

# Useful tools

# Disadvantages

# Conclusion

# Links