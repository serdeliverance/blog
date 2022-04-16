---
title: TODO TODO TODO
excerpt: 'TODO TODO TODO'
categories:
  - blog
tags:
  - kafka
  - notes
header:
  image: '/images/header.jpg'
---

# Intro

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