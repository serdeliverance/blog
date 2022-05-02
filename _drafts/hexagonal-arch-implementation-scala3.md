---
title: Hexagonal Architecture with Scala 3 and Cats Effect
excerpt: 'TODO TODO TODO TODO TODO'
categories:
  - blog
tags:
  - scala
  - microservices
  - architecture
  - cats
header:
  image: '/images/header.jpg'
---

# Intro

# Hexagonal architecture

`TODO`

# Tagless final

# The business case

# Project setup

Let's create our project using the following `giter8` template

``` scala
sbt new scala/scala3.g8
```

Just to showcase, our final project structure will be something like this:

`TODO image screenshoot project structure`

# Domain layer

Let's start defining our `domain`. First, create a package called `domain`, and inside it, a package called `entities`. Inside this package, we will create our `account` entity. It will have the following attributes:

``` scala
package io.github.hxarchdemo.domain.entities

import java.time.OffsetDateTime

case class Account(
    name: String,
    description: Option[String],
    userId: Long,
    initialAmount: BigDecimal,
    createdAt: OffsetDateTime,
    id: Option[Long] = None
)
```

It has a `name`, an optional `description`, an associated `userId` (maybe related with another microservice which takes care of user related data), an `initial amount` and an `id`, etc.

Now, let's define the `use cases` it exposes to the outer layers. For that, lets create a `package` called `usecases` inside the `domain` `package`.

Inside this package going to create our first `use case`: `GetAccountUseCase` in a dedicated file. It will expose the following algebra:

``` scala
trait GetAccountUseCase[F[_]]:
  def getAccount(): F[Option[Account]]
```

Then, we will do the same for the `GetAllAccountsUseCase` and `CreateAccountUseCase`:

``` scala
trait GetAllAccountsUseCase[F[_]]:
  def getAllAccounts(): F[List[Account]]
```

``` scala
trait GetAccountUseCase[F[_]]:
  def getAccount(): F[Option[Account]]
```

We have our domain defined. It is interesting to note that, using this approach (more specifically, the `tagless final` one), our domain is library agnostic. We are not tied to any `effect system`.

Our folder structure is taking now the following shape:

`TODO UPDATED screenshoot of the folder structure`

# Application layer

The `application layer` is in charfe of implementing the business logic and wired the remaining layers together. More specifically, it communicates the adapter layer with the domain one through ports.

# Adapters

# Adding more adapters

# Wiring all together

`TODO create server instantiating resources`

# Refactor #1: Improving account retrieving using cache

`TODO`

`TODO alternative defining a class CachedRepository with implements the same trait`

# Refactor #2: Changing db library

`TODO changing doobie by skunk`

# Refactor #3: adding grpc adapter

# Testing

# Improvements

# Conclusion