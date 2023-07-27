---
title: Kafka 101
excerpt: 'TODO TODO TODO'
categories:
  - blog
tags:
  - kafka
header:
  image: '/images/header.jpg'
---

# Intro

## What is Kafka?

TODO: what is kafka?

TODO: who uses it?

## Kafka use cases

## Kafka vs Rabbit MQ vs others

- message replay by default
- built for parallelism

## Delivery semantics

## Noooootessss

## Producers

key: los producers saben de antemano a cuál partition enviar un mensaje. Ademas de eso, existe una property importante, que es la `key` del mensaje. Esta key es opcional, entonces:

- si key == null => los mensajes se van enviando a cada partition estilo `round robin`

- si key != null => sobre la key se aplica un `hash` y esto determina a qué partition va a ir el mensaje. Recordar también que `los mensajes están ordenados dentro de una partición`.

- Kafka message anatomy:

![kafka message anatomy]({{ site.baseurl }}/assets/images/kafka-notes/kafka-message-anatomy.png "Kafka Message Anantomy")

- kafka solo acepta bytes as an input de los producers, y envía bytes como output a los consumers

- entonces, serializacion es el proeso de convertir objects en bytes

- `kafka partitioner` es la lógica que determina a qué `partition` de `topic` va a ir un determinado mensaje. `Key Mapping` es el proceso de terminar el mapeo de una key contra una parittion.

Ejemplo de la función de `hashing` default de kafka:

``` javascript
targetPartition = Math.abs(Utils.murmur2(keyBytes)) % (numPartitions - 1)
```

Entonces, está es la forma en la que con `Producer` conoce de antemano, a cual partition va a enviar un mensaje.

## Consumers

- los consumers implementan el `pull model` => esto significa que no es el broker el que pushea la data a los consumers, sino que estos últimos son los que pullean del broker.

## Consummer Group

Los consumers leen data a través de `Consumer Groups`. `Consumer Groups` es una forma de agrupar consumers. Cada consumer dentro de un consumer group lee de una partición distinta.

![consumer groups]({{ site.baseurl }}/assets/images/kafka-notes/consumer-groups.png "Consumer Groups")

`Es posible tener multiple consumer groups sobre una misma particion`

![multiple consumer groups]({{ site.baseurl }}/assets/images/kafka-notes/multiple-consumer-groups.png "Multiple Consumer Groups")

- group.id

## Consumer offsets

Kafka guarda los offset que los consumers groups han leido

Dichos offsets commiteados se almacenan en un `topic` especial llamado `__consumer_offsets`

Los consumers deben commitear periódicamente los offsets de los mensajes que hayan procesado. Deben hacerlo cada consumer dentro del consumer group (es decir, no es algo que se realiza a nivel consumer group)

## Delivery semantics for consumers

Java consumers automatically commits offsets (`at least once`)

3 delivery semantics:

- `At least once (recomendado)`
  - Offsets son commiteados luego de que el mensaje es procesado.
  - si falla el processing, el mensaje se lee de nuevo (te da mas control ante fallos)

- `At most once`
  - Se commitea el Offset ni bien el mensaje llega al consumer
  - Si el procesamiento falla, se pierde el mensaje (dado que ya commiteaste el offset)

- `Exactly once`
  - generalmente puede obtenerse esta garantía cuando usamos `Kafka Streams API`, dado que usa por debajo la `Transactional API` (que provee estas garantias)
  - O usando un idempotent consumer (lo tenés que crear a manopla)

## Kafka broker

A kafka cluster is composed of multiple brokers (servers)

Each broker is identified with its ID

Each broker contains certain topic partition

## Kafka Broker Discovery

cada broker en el cluster es también conocido como `bootstrap server`

esto significa que solo es necesario conectarse con un broker, y así poder conectarse con todo el cluster (a esto se lo conoce en kafka como `smart clients`).

![kafka broker discovery]({{ site.baseurl }}/assets/images/kafka-notes/kafka-broker-discovery.png "Kafka Broker Discovery")
