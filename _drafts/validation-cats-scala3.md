---
title: Validation with Cats in Scala 3
excerpt: 'Validation entity example in Cats using Scala 3 features'
categories:
  - blog
tags:
  - scala
  - cats
header:
  image: '/images/header.jpg'
---

# Intro

In this post we are going to see how to validate entities using `Cats` in `Scala 3`. Also, it is a great opportunity to see how extension methods are used and opaque types are used.

# A quick intro to Validation

Imagine that we have an `API` for managing accounts. For this example, we are going to use the following entity:

``` scala
case class Account(
    name: String,
    userId: Long,
    initialAmount: BigDecimal,
    createdAt: OffsetDateTime,
    id: Option[Long] = None // id is assigned after creation in the db
)
```

We expose and endpoint that allows users to create accounts. This endpoints receives the following `DTO`:

``` scala
case class AccountDTO(
  name: String,
  userId: Long,
  initialAmount: BigDecimal,
  createdAt: OffsetDateTime
)
```

An `account` to be valid needs to satisfy the following rules:

- `name` must not be empty
- `initialAmount` must be positive
- `userId` must be positive
- `createdAt` must be less than current time

In order to properly validate those fields, our first approach could be something based on `Either`, which is a datatype that allow us to represent a computation that can be either an error or a success. So, we create the following methods:

``` scala
def validateName(name: String): Either[String, String] =
  Either.cond(name.nonEmpty, name, "name must not be empty")

def validateUserId(userId: Long): Either[String, Long] =
  Either.cond(userId > 0, userId, "userId must be positive")

def validateInitialAmount(initialAmount: BigDecimal): Either[String, BigDecimal] =
  Either.cond(initialAmount > 0, initialAmount, "initial amount must be positive")

def validateCreatedAt(createdAt: OffsetDateTime): Either[String, OffsetDateTime] =
  Either.cond(createdAt.isBefore(OffsetDateTime.now), createdAt, "creation date could not be in the future")
```

`Either` is a monad, so we can evaluate all of this rules using a `for-comprehension`:

``` scala
def validate(accountDTO: AccountDTO): Either[String, Account] =
    for
      name          <- validateName(accountDTO.name)
      userId        <- validateUserId(accountDTO.userId)
      initialAmount <- validateInitialAmount(accountDTO.initialAmount)
      createdAt     <- validateCreatedAt(accountDTO.createdAt)
    yield Account(name, userId, accountDTO, createdAt)
```

The problem with that approach is that `Either` short-circuits the operation at the first error (`Left` value) it gets. So, for example, if we have the following:

``` scala
val accountDTO = AccountDTO(
    "pedro",                        // OK
    -20,                            // invalid userId
    -500,                           // negative initial amount
    OffsetDateTime.now.plusYears(2) // creation date is two years in the future!!!
  )

  val result = validate(accountDTO)

  println(result) // it will print `Left(userId must be positive)`
```

Just the first invalid result is informed. If the user fixes this error, she will have to retry until all the remaining fields were ok, which is a really bad experiencie for an `API end user`. In this case, it would be great to have a way to express all the invalid values at once. Let's try to find another `Datatype` that help us to reach that goal.

# Validated

`TODO Validated intro`

Before moving into Validated, let's do a little refactor. Let's typify our validations.

``` scala
sealed trait AccountValidation:
  def errorMessage: String

case object NameIsEmpty extends AccountValidation:
  override def errorMessage = "name must not be empty"

case object UserIsInvalid extends AccountValidation:
  override def errorMessage = "userId must be positive"

case object InitialAmountNotPositive extends AccountValidation:
  override def errorMessage = "initial amount must be positive"

case object CreationDateInvalid extends AccountValidation:
  override def errorMessage = "creation date could not be greater than current time"
```

Now, let's continue with the use of `Validated`

`TODO using Validated`


# An improvement

# Bonus: testing our validations

# Conclusions