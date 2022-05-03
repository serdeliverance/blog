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

Inside this package going to create our first `use case`: `CreateAccountUseCase` in a dedicated file. It will expose the following algebra:

``` scala
trait CreateAccountUseCase[F[_]]:
  def createAccount(account: Account): F[Account]
```

Then, we will do the same for the `GetAccountUseCase` and `GetAllAccountsUseCase`:

``` scala
trait GetAccountUseCase[F[_]]:
  def getAccount(): F[Option[Account]]
```

``` scala
trait GetAllAccountsUseCase[F[_]]:
  def getAllAccounts(): F[List[Account]]
```

We have our domain defined. It is interesting to note that, using this approach (more specifically, the `tagless final` one), our domain is library agnostic. We are not tied to any `effect system`.

Our folder structure is taking now the following shape:

`TODO UPDATED screenshoot of the folder structure`

# Application layer and the implementation of CreateAccountUseCase

The `application layer` is in charge of implementing the business logic and wire the remaining layers together. More specifically, it communicates the adapter layer with the domain one through ports.

Let's create a package called `application` at root level. Inside it, we are going to create a file called `CreateAccountService`, which will implement the `CreateAccountUseCase`. It will be our first draft implementation:

``` scala
package io.github.sdev.mpf.application

class CreateAccountService[F[_]] extends CreateAccountUseCase[F]:
  override def createAccount(account: Account): F[Account] = ???
```

For the purpose of this tutorial, we will define a dummy implementation that just persist into database through a repository without taking care of validation. However, the [final code](https://google.com) uses [ApplicativeError and MonadErrror](https://typelevel.org/cats/typeclasses/applicativemonaderror.html) for performing validations at a Higher Kind level.

Comming back to our implementation, we need a repository to perform changes against the db. We need some kind of `AccountRepository`. If we think about it, it will be some kind of external component from the point of view of our application layer:

![Account Repository port]({{ site.baseurl }}/assets/images/hexagonal-arch-scala3-ce/XX-account-repository.png "Account Repository port")

So, lets define a `package` called `ports` inside `application`. Inside this new package, let's create another one called `out` (because with this port, the communication flow will be from inner layers towards the outer ones). Finally, let's define the algebra of our repository:

``` scala
package io.github.sdev.mpf.application.ports.out

trait AccountRepository[F[_]]:
  def save(account: Account): F[Account]
```
For now it is ok. Later, we will implement this feature on the correspondent adapter. Let's come back to `CreateAccountService` and finalize the implementation of our use case:

``` scala
class CreateAccountService[F[_]](accountRepository: AccountRepository[F]) extends CreateAccountUseCase[F]:

  override def createAccount(account: Account): F[Account] =
    accountRepository.save(account)
```

This implementation is dummy, but reflects the dependency with our colaborator (`AccountRepository`)


I leave the implementation of `GetAccountUseCase` as an exercise for the reader. However, you can check it out in the [repo](https://google.com)

# Adapters

# Implementing GetAllAccountsUseCase

# Adding more adapters and implementing the missing ones

# Wiring all together

`TODO create server instantiating resources`

# Refactor #1: Improving account retrieving using cache

`TODO`

`TODO alternative defining a class CachedRepository with implements the same trait`

# Refactor #2: Changing db library

`TODO changing doobie by skunk`

# Refactor #3: adding kafka adapter

# Testing

# Improvements

# Conclusion