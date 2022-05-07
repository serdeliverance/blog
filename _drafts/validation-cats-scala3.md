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

`Either` is a monad, so we can evaluate all this rules using a `for-comprehension`:

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

Just the first invalid result is informed. If the user fixes this error, she will have to retry over and over again until all the remaining fields were ok, which is a really bad experience for an `API user`. In this case, it would be great to have a way to express all the invalid reasons at once. Let's try to find another `datatype` that can help us to reach that goal.

Before moving to the next section, let's do a little refactor to typify our validations.

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

# Validated

[Validated](https://typelevel.org/cats/datatypes/validated.html) is a datatype provided by `Cats` that is similar to `Either` because it can take two possible values: `Valid` or `Invalid`:

``` scala
sealed abstract class Validated[+E, +A] extends Product with Serializable {
  ???
}

final case class Valid[+A](a: A) extends Validated[Nothing, A]
final case class Invalid[+E](e: E) extends Validated[E, Nothing]
```

We can refactor our validations to use `Validated` instead.

``` scala
import cats.data._
import cats.data.Validated._
import cats.implicits._

def validateName(name: String): Validated[AccountValidation, String] =
  Either.cond(name.nonEmpty, name, NameIsEmpty).toValidated

def validateUserId(userId: Long): Validated[AccountValidation, Long] =
  Either.cond(userId > 0, userId, UserIsInvalid).toValidated

def validateInitialAmount(initialAmount: BigDecimal): Validated[AccountValidation, BigDecimal] =
  Either.cond(initialAmount > 0, initialAmount, InitialAmountNotPositive).toValidated

def validateCreatedAt(createdAt: OffsetDateTime): Validated[AccountValidation, OffsetDateTime] =
  Either
    .cond(createdAt.isBefore(OffsetDateTime.now), createdAt, CreationDateInvalid)
    .toValidated
```

The problem with this approach is that we cannot use our former `validate` method (which is based on `for-comprehensions`), because `Validated` is not a Monad, so it doesn't have `flatMap`. Instead, `Validated` is an [Applicative Functor](https://typelevel.org/cats/typeclasses/applicativetraverse.html). We have to apply some refactors to this method.

# Using Applicative

`for-comprehensions` are `fail-fast`, which is not suitable for our use case. We need to find a way to accumulate our errors. In this case, we can try to use another structure to express the invalid side of our `Validated`:

``` scala
type ValidationResult[T] = Validated[NonEmptyList[AccountValidation], T]
```

`NonEmptyList` is a data structure that guarantees that it will hold at least one element, which is what we are looking for in case of having validation errors.

`ValidationResult[T]` is a convenient alias for re-utilize this kind of type definition.

Also in `Cats`, the data type `Validated[NonEmptyList[DomainError], A]` has an alias: `ValidatedNel[DomainError, A]`. So, we can rewrite our definition:

``` scala
type ValidationResult[T] = ValidatedNel[AccountValidation, T]
```

Having all this set up, we can refactor our validation methods:

``` scala
def validateName(name: String): ValidationResult[String] =
  if name.nonEmpty then name.validNel else NameIsEmpty.invalidNel

def validateUserId(userId: Long): ValidationResult[Long] =
  if userId > 0 then userId.validNel else UserIsInvalid.invalidNel

def validateInitialAmount(initialAmount: BigDecimal): ValidationResult[BigDecimal] =
  if initialAmount > 0 then initialAmount.validNel else InitialAmountNotPositive.invalidNel

def validateCreatedAt(createdAt: OffsetDateTime): ValidationResult[OffsetDateTime] =
  if createdAt.isBefore(OffsetDateTime.now) then createdAt.validNel else CreationDateInvalid.invalidNel
```

`validNel` and `invalidNel` are syntax methods provided by the `Validated` object (`import cats.data.Validated._`). Those methods allow us to lift our values to `ValidatedNel` context.

Also, it is a great oportunity to see how clean flow control expressions are in `Scala 3` (you can see it in the `if-then-else` expressions above).

Now, we can rewrite our `validate` method, but using this time `Applicative` syntax, more specifically, the `mapN` method.

``` scala
def validate(accountDTO: AccountDTO): ValidationResult[Account] =
    (
      validateName(accountDTO.name),
      validateUserId(accountDTO.userId),
      validateInitialAmount(accountDTO.initialAmount),
      validateCreatedAt(accountDTO.createdAt)
    )
      .mapN((name, userId, initialAmount, createdAt) => Account(name, userId, initialAmount, createdAt))
```

`mapN` allow us to apply all the validations, returning the success values as a `tuple`, or the accumulated validation error list.

Let's see how it works:

``` scala
val account = Account("n26-acc", -10, -500, OffsetDateTime.now.plusDays(2))

val result = validate(account)

println(result) // it will print: Invalid(NonEmptyList(UserIsInvalid, InitialAmountNotPositive, CreationDateInvalid))
```

So, we achieved our goal of having an accumulated list of valition errors in case of failure.

# Some words about Applicative and mapN

`mapN` uses [Semigroupal](https://typelevel.org/cats/api/cats/Semigroupal.html) under the hood. More specifically, the method `product` from `Semigroupal`. This method allow us to combine two effects `F[A]` and `F[B]` into `F[(A, B)]`.

In case of `Validated`, the `product` method accumulate validation errors if found any. Otherwise, it returns the product (`Valid[(A, B, C, ...)])`).

You can found more info about `Applicative` in [Cats documentation](https://typelevel.org/cats/typeclasses/applicative.html). Also, the book [Essential Effects](https://essentialeffects.dev/) brings a great intro to this topic and its importance when applying to parallelism scenarios.

# An improvement

We can enrich our `Account` case class adding `validate` as an extension method. For doing that, we can take advantage of `Scala 3` extension methods:

``` scala
object Account:

  extension (account: Account)
    def toValidated: ValidationResult[Account] =
      (
        validateName(account.name),
        validateUserId(account.userId),
        validateInitialAmount(account.initialAmount),
        validateCreatedAt(account.createdAt)
      )
        .mapN((name, userId, initialAmount, createdAt) => Account(name, userId, initialAmount, createdAt))
```

This can allow us to have a cleaner syntax when performing validations in other places of our code:

``` scala
def create(account: Account): IO[Account] =
  account.toValidated match
    case Valid(account) => repository.save(account)
    case Invalid(erros) => IO.raiseError(???) // your error handling logic/reporting
```

# Bonus: testing our validations

Following, there is our test suite. It is a very regular one using just plain `Scalatest`:

``` scala
class AccountValidatorSpec extends AnyFlatSpec with Matchers {

  private val account = Account("n26-23", 34, 23, OffsetDateTime.now)

  it should "return valid account" in {
    val result = account.toValidated
    result mustBe Valid(account)
  }

  it should "return invalid userId" in {
    val invalidAccount = account.copy(userId = -5) // invalid userId
    val result         = invalidAccount.toValidated
    result match
      case Invalid(nel) => nel.toList mustBe List(UserIsInvalid)
      case _            => fail("userId validation error expected")
  }

  it should "return invalid accumulated list results" in {
    val invalidAccount =
      account.copy(userId = -5, createdAt = OffsetDateTime.now.plusYears(2)) // invalid userId and createdAt
    val result = invalidAccount.toValidated
    result match
      case Invalid(nel) => nel.toList mustBe List(UserIsInvalid, CreationDateInvalid)
      case _            => fail("multiple account validation error expected")
  }
}
```

# What the F[_]???

During this tutorial we were talking about data validation using `Validated`, which is an specific data structure. However, you can also abstract over the concept of validation and not being tied to any particular implementation. This last approach is more evident when your code is strongly based on `polymorphic effects`. In cases like that, you can check [ApplicativeError and MonadError](https://typelevel.org/cats/typeclasses/applicativemonaderror.html#).

# Conclusions

In this post we have seen how we can use `Validated` datatype from `Cats` to validate our domain objects and how we can use it in `Scala 3`. The code is available on [github](https://github.com/serdeliverance/sc-blog-code/tree/master/validation-cats-scala3)