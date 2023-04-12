---
title: Implicit Conversions in Scala 3
date: 2023-04-12 20:19:00
excerpt: 'how to do implicit conversions in Scala 3'
categories:
  - blog
tags:
  - scala
  - scala 3
header:
  image: '/images/header.jpg'
---
## Intro

## Implicit conversions in Scala 2

Imagine you have the following class:

``` scala
class Person(name: String):
  def greet: String = s"Hi, $name"
```

And the following `implicit conversion`:

``` scala
// Implicit conversion using Scala 2 style
implicit def string2Person(string: String): Person =
  new Person(string)
```

You'll be perfectly able able to do the following and the compiler won't complain:

``` scala
val pepe: Person = "pepe"
"Maluma".greet
```

That is possible, because the compiler will look for an `implicit conversion` in scope and, once it finds one, it will apply it to convert from one type to the desired one. Basically, it will do the following (commented code):

``` scala
val maluma: Person = "Maluma" // string2Person("pepe")
"Maluma".greet // string2Person("Maluma").greet
```

As you can see, this mechanism has some disadvantages. For example: the code more difficult to understand as there is lot of magic going on behind the scenes (finding the `implicit conversion` that makes trick can be very difficult if you have lot of imports). Also, it is not clear which methods you can call over a specific type (for example: you'll not have any idea which other methods apart from `greet` can be called over the type `String` in the example above).

## Implicit Conversions in Scala 3

`Scala 3` follows the approach of ***focusing on intent over mechanism*** (as they stated in the [official documentation](https://docs.scala-lang.org/scala3/new-in-scala3.html)). So, the usage of `implicit conversions is discouraged`. However, if you want to use it, you should follow the following steps:

1. import implicit conversion support.
2. define a given value of type conversion.

Refactoring our previous example to follow those steps, it will look like the following:

``` scala
import scala.language.implicitConversions

given string2Person: Conversion[String, Person] with
  override def apply(string: String): Person =
    new Person(string)
```

So, `Scala 3` forces you to be explicit regarding `implicit conversions`.

## Conclusions

`Implicit Conversions` is a mechanism that allows a type to be implicitly converted into another one. However, this mechanism has some downsides and it is not encouraged in `Scala 3`. Because of that, `Scala 3` forces you to be explicit regarding `implicit conversions`. That way, it ensures yo to know what you are doing and also helps you to write cleaner code that is easier to understand and maintain the future.
