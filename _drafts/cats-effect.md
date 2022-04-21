---
title: Cats Effect
excerpt: 'TODO TODO TODO TODO TODO'
categories:
  - blog
tags:
  - scala
  - cats
header:
  image: '/images/header.jpg'
---

# Intro

`disclaimer`: This post is strongly based on the [Cats Effect documentation](https://typelevel.org/cats-effect/docs/getting-started) and the [Essential Effects book](https://essentialeffects.dev/) by Adam Rosien.

# What are Effects?

`effect` is a description of an action. Here, the description is separated from evaluation. It give us more control and also allow us to handle effects as values.

`side-effect` is something that happens `on the side`. For example

``` scala
def double(x: Int): Int = {
  System.out.println("hello, world!")
  x + x
}

```

Here, we are not just returning the sum, we are also performing a `side effect`, which is printing to the console (it is an action that happens on the side and that has an interaction with the outside world).

Example of side effects: changing the value of a `var`, making a network call, logging, etc.

Generally, a `side effect` is an action that just happens. You don't have control over it. So, it is not possible to evaluate it in parallel, or on a different thread pool, or makes retries.

# Implementing an IO

# The IO monad from Cats

# How to manage side effects on Cats Effect

In `Cats Effect`, code that contains `side effects` should be wrapped using special constructors:

- `Synchronous` (effects with `returns` or `throws`)
  - `IO(...)` or `IO.delay(...)`
  - `IO.blocking(...)`
  - `IO.interruptible(...)`
  - `IO.interruptibleMany(...)`

- `Asynchronous` (which invokes a callback)
  - `IO.asyc` `IO.asyc_`

That way we convert the `side effect` into an `effect`, making them a description of an action, which allow us to have more control over it execution.

``` scala
val wrapped: IO[Unit] = IO(System.out.println("Hello, world!"))
```

This make it possible to take advantage of the powers of composability and `functional programming`.

# IO niceties

# The Tagless Final approach

# IO Runtime

# Real world example

# Conclusion