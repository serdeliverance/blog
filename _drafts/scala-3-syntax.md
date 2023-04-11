---
title: Scala 3 new syntax
excerpt: 'Reviewing the new Scala 3 syntax for basic constructs'
categories:
  - blog
tags:
  - scala
header:
  image: '/images/header.jpg'
---

## Intro

In this tutorial, we are going to review the new `Scala 3` syntax of basic constructs such as `if`, `while`, `for-comprehension`, `pattern matching`, etc and the new `identation` mechanism.

## if expressions

As lot of constructs in `Scala 3`, if expressions took lot of inspiration from `Python`.

```scala
val ifExpression = if 2 > 3 then "bigger" else "smaller"
```

We can see that parentesis aren't required any more, but you need to use the keyword `then` to indicate the beginning of the left branch of the if expression.

If one of the branches of the expression has multiple lines, you can rely on `identation`:

```scala
val anotherExpression =
  if 2 > 3 then
    val result = "code"
    // more stuff
    result  
  else
    val result = "smaller"
    // code
    result
```

Also, it is really important to mention that `Scala 2` code is valid syntax for `Scala 3`. So you can still use traditional if expression the same way as in `Scala 2`.

``` scala
// Scala 2 if expression
val ifExpression = if (2 > 3) "bigger" else "smaller"

val ifExpression_v3 =
  if (2 > 3) {
    val result = "code"
    // code
    result
  } else {
    val result = "other result"
    // code
    result
  }
```

## Braceless and significant identation

You can add `end` token basically for any kind of block that has significant identation.

## Conclusions

`Scala 3` feels like a new programming language who takes lot of inspiration from `Python` but, at the same time, keeps all the good things `Scala` has while preserving its essence too. It is awesome having this new syntax that makes the language simpler and more expressive, but keeping all the amazing things it already has, such a `pattern matching`, `for-comprehensions`, `lambdas`, `currying`, to mention a few. I'm really looking forward to experimenting with all those new stuff by writing `JVM` applications using `Cats Effect` and `ZIO`.
