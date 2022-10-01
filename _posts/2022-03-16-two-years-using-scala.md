---
title: Two years using Scala
date: 2022-03-16 21:30:00
excerpt: 'Thoughts about my journey into this awesome language and its ecosystem'
categories:
  - blog
tags:
  - scala
header:
  image: '/images/header.jpg'
---

## Intro

It's been more than a two years since I started learning `Scala`. So, I think it's a good time to review my journey and share my thoughts about this awesome language and its ecosystem.

`Disclaimer`: I'm a traditional backend developer that have no knowledge about `Spark`, so I won't review it in this post. I hope I'll do in the future.

## My journey

My first contact with `Scala` was through the book [Functional Programming in Scala](https://www.manning.com/books/functional-programming-in-scala) (aka `The Red Book`). That book helped me to learn some functional programming ideas and basic `Scala` syntax. Some months after reading it, I realized it was not the best way to getting started with the language (it was extremely challenging). Their exercises were very difficult and makes you think over them for a while until you came up with the solution.

I must confess I never finished that book, but it gives me a good taste of the `Scala` syntax. My next resource was [Akka Essentials with Scala Udemy's course](https://www.udemy.com/course/akka-essentials/) by `Rock the JVM`. This course was awesome and I had a great time learning from it. Then I continued with the `Akka Persistence` and the `Akka Streams` courses from the same author. That last one, simply blows my mind. Writing async pipelines were awesome and I felt in love with Stream processing, in general, and Akka Streams and Alpakka, in particular.

I strongly recommend diving a little bit into `Àkka` and `Akka Streams`, because I think that learning actors concurrency model and stream processing simply opens your mind to new solutions.

After that, I got my first job as a `Scala` developer, and I started learning more about `Play Framework` and I had my first contact with `Typelevel libraries`, such as `Cats` and `Circe`.

Also, I continued reading some books in my free time . I read `Scala Essentials`, `Scala with Cats` and I've tried to develop some applications using just the `Typelevel stack` (`Cats`, `Http4s`, `Doobie`, etc) but I've never succeeded. Until now I still think that `Typelevel` ecosystem is very challenging and over complicated.

In the last couple of months, I've been studying `ZIO`, `Cats` and `Scala 3`.

## Language

`Scala` is by far, my favourite programming language. It has a great mix between `OOP` and `Functional Programming`, and great concurrency libraries and tools (`Akka`, `Typelevel`, `ZIO`, etc).

Some things that I love from this language are:

- functions as first class citizens

- pattern matching

- case classes

- immutability in general

- lazy vals

- implicits (but without abusing of it)

- its Collections API

- monads and for comprehensions

- `Future` (I know it is heated, but its a simply way of implementing concurrency)

- tail recursion

- type alias

- Typeclasses (without abusing of it)

- functional programming libraries without being a `FP fundamentalist`

- the `Akka` ecosystem.

Also, the code is less verbose than `Java` and `Kotlin` (IMHO). I think the language is very flexible and elegant.

## Community

Having a language that is so flexible could lead to have different approaches to solve the same problem. Because of that, different `schools` have been emerged in the `Scala` ecosystem.

At a first sight I could identify four `Scala` schools: the Odersky's one, `Typelevel`, `ZIO` and the Lil Haoyi's one. Let's review each of them.

### The Odersky's school (aka the Lightbend approach)

Martin Odersky is the creator of the `Scala` language, and this school follows his idea: having a mix between `OOP` and `Functional Programming`. I think his vision was achieved and the language itself is awesome. Things like inmutability, lazy evaluation, case classes, traits, implicits, the collections API, monads and for comprehensions, and Futures are simply great and allows you to solve a wide sort of problems in a very elegant way. Additionally, you have `Akka` and `Play`, which gives you more tools for implement high performance services and introduce you to different concurrency models (`CPS`, `Actors`, `Streaming`, `Distributed programming`).

I think diving into this approach is the easiest way to learn `Scala`. Also, learning the `Lightbend stack` (`Akka`, `Akka Cluster`, `Akka Streams`, etc) gives you lots of insights about architecture and design. Because of that, it is not strange that [big companies](https://www.lightbend.com/case-studies), such as `Pay Pal`, `Wallmart`, `MOIA`, `Tesla`, `Tubi` or even [Fortnite](https://www.lightbend.com/blog/akka-powers-400-million-users-streaming-gaming-entertainment) choose those technologies.

However, given the flexibility of `Scala` plus the rich and solid `JVM` ecosystem, new approaches has been emerged. Many of them are very influenced by `Haskell` and try to implement pure functional programming on the `JVM`.

### Typelevel school

[Typelevel](https://typelevel.org/) takes ideas from `Haskell` and `Type safety` but in another level and you can see that influences in their core libraries (`Cats` and `Cats Effect`), and also in libraries that are based on them, such as `Doobie`, `Http4s` and `Circe`. Those libraries are very concerned about how to deal with `side effects` in a safer way, and also, they are very concerned about `Type Safety` (but sometimes, too much IMHO). I must confess this approach is very interesting but sometimes it gets very difficult to understand. For example, let's see the following code:

``` scala
def allRoles[F[_], Auth](
    pf: PartialFunction[SecuredRequest[F, User, AugmentedJWT[Auth, Long]], F[Response[F]]],
)(implicit F: MonadError[F, Throwable]): TSecAuthService[User, AugmentedJWT[Auth, Long], F] =
  TSecAuthService.withAuthorization(_allRoles[F, AugmentedJWT[Auth, Long]])(pf)
```

I think the approach of abstracting over the effect system and define interpreters is cool, but trying to be ultra type safe is not beginner friendly and engornomic. If you can have a good balance between abstraction and code readability, it has a great ecosystem and it is a very enjoyable approach.

Typelevel has a lot of resources to learn and its community is very active and friendly. Reading its docummentation is always insightful and joining to its `Discord` channel is worthwhile (there are lots of brillant people there that are very pleased to help you if you have any question).

### ZIO

The [ZIO](https://zio.dev/) community is reaching lot of traction during the last couple of years. `ZIO` is an effect system for asynchronous computations, whose approach is to use idiomatic `Scala` code without abusing of implicits and type classes. Its entry barrier is very low compared with the `Typelevel` one. Also, you can see a `ZIO` as a `Future` with steroids.

Its community is very active and charmfull. The `ZIO` vision is to provide libraries for writting `Pure Functional` and concurrent programs using idiomatic `Scala`.

Nowadays, I'm not considering using `ZIO` for full backend development as I do with `Typelevel` or `Play/Akka/Future`, because I consider it is too much low level sometimes. But that's just my noob opinion. However, I think it is a great option for building tools, such as [Greyhound](https://github.com/wix/greyhound) or [Caliban](https://github.com/ghostdogpr/caliban).

Also, the `ZIO` ecosystem was one of the firsts in adopting `Scala 3`.

### The Python approach

Another `Scala` school is the [Li Haoyi's one](https://www.lihaoyi.com/). He took some ideas from `Python` and translated them to the `Scala` world. You can see those ideas in the [ecosystem of libraries and tools](https://github.com/com-lihaoyi) that he created, which are focused on usability and efficiency. Its libraries are beginner friendly and really useful. I really want to experiment with them and also give a try to [Ammonite](https://github.com/com-lihaoyi/Ammonite).

You can also read [his book](https://www.handsonscala.com/) and this [interesting talk](https://www.youtube.com/watch?v=UiN6yZPAYww&t=454s) about how `Scala` development is approached at `Databricks` (the company where he works in).

Summarizing, I think that having different ways of thinking is a bless because it gives you different tools and approaches for solving problems, but it is very important to define some guidelines or standarization for styling when working on a big team or a big codebase. Also, I feel that pragmatism and cleanest are the two main concerns that every developer should have in mind when developing software.

## Tooling

From my point of view, the tooling in `Scala` is not the best. [SBT](https://www.scala-sbt.org/) is its recommended build tool. It is slow and extremeley complex. It has plugins, but most of them have very poor, and difficult to understand, documentation.

Talking about IDEs and editors, `IntelliJ` has a good support for `Scala 2` but it is very slow sometimes (just use it with `Play Framework` and tell me what you think). In `Visual Studio Code` you can use [Metals Plugin](https://scalameta.org/metals/) which is very fast in comparison with `IntelliJ` (mainly, because you use your native `SBT` installation instead of an embedded one, as `Intellij` does), but I think it is very buggy sometimes (the `Metals` plugin stops frequently) and doesn't have great refactor utilities as good as `IntelliJ`.

## Scala 3

A brief mention to `Scala 3`. It is the newest version of `Scala`, with brings a new syntax and tries to bring to the table the essentials of `Scala` (which is `Dott calculus`, because of that the compiler for `Scala 3` was named `Dotty`).

I think the new version of `Scala 3` is great. It tries to empathise explicitness over mechanisms (for example, in the `contextual abstractions`). Also, having optionally braces is great.

This new version brings features to the `Type System` (such as `Union` and `Interception` types, `Match Types`, etc), and also new tools for metaprogramming (such as `inline`). It brings new tools for functional programming too (such as `Context Functions`). I think that all of these features make the language more interesting and funny to learn. However, I'm afraid of this could lead to lot of code, tools and documentation with examples harder to understand and not beginner friendly. I've seen that the `Scala` community tends to abuse of advanced mechanisms and uses it in a way that makes you feel that they are the norm, making the language sharp and innecesary complex.

## Next steps in my journey

Nowadays, I feel very excited with `Typelevel` and `Scala 3` stuff. I'm scratching the surface of `Tagless Final` and I'm really enjoying all the `Typelevel` libraries around its ecosystem. Also, I feel very interesting in `Scala 3` new features, syntax and specific topics such as [Type Class Derivation](https://docs.scala-lang.org/scala3/reference/contextual/derivation.html).

## Conclusions

Scala is a great language with a great ecosystem. It has great tools for concurrency and functional programming. Also, there are lots of smart people to learn from. So, If you are a Java developer and you want to grow, I strongly recommend to give it a try. All the things that I learned during this journey made me growth as a developer.
