---
title: Semigroupal and Applicative
excerpt: 'Explanation about Semigroupal and Applicative Typeclasses'
categories:
  - blog
tags:
  - scala
  - cats
header:
  image: '/images/header.jpg'
---

# Intro

In this post we are going to see what `Semigroupal` and `Applicative` typeclasses are and why they are useful.

# Functor and Monad for sequencing competitions

`Functor` and `Monad` are type classes that abstracts over the concept of sequencing operations, which their methods `map` and `flatMap`, respectively. This way of sequencing operations is `fail-fast` in the way it deals with errors. It is useful it lot of cases. For example:

``` scala
def createAccount(account: Account): IO[Account] =
  for
    _       <- validate(account)
    account <- repository.save(account)
  yield account
```

If `validate` fails, then the next step is not executed.

However, there exists some use cases when we need more flexibility. For example, in `Form Validation` we need to perform all validations and accumulate their error results in order to give accurated information to the end user. Another example could be running independent tasks concurrently.

`Semigroupal` and `Applicative` are the typeclasses that allows this kind of flexibility.

# Semigroupal

`Semigroupal` abstracts over the idea of `join contexts` through the method `product`. Following is the definition of `Semigroupal` in `Cats`:

``` scala
trait Semigroupal[F[_]] {
  def product[A, B](fa: F[A], fb: F[B]): F[(A, B)]
}
```

So, given two values `F[A]` and `F[B]`, `Semigroupal` allows to join them in a tuple `F[(A, B)]`. It is important to mention that `F[A]` and `F[B]` are two independent values that could be computed independently (in contrast with a monadic structure, that enforces sequencing).

# Applicative

`nota de cats`

```
Applicative extends Semigroupal and Functor . It provides a way
of applying func ons to parameters within a context. Applicative is
the source of the pure method we introduced in Chapter 4.
```

# Why it is useful?