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

# Concurrency vs Parallelism

# Threads and Locking

- threads and locking
- deadlock

# Synchronous vs Asynchronous

- ejemplo service de checkout que tiene el siguiente flujo: pega a una third party api para autorizar el pago, obtiene info adicional del usuario de cache (redis), guarda transaccion en la base, emite un evento (para el sistema de logistica entregue el pedido y para que el sistema de envío de mail envie el mail al usuario)

- sync vs async

# CompletableFuture

# Review the previous example

# Conclusion