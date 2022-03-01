---
title: Two years using Scala
excerpt: 'Thoughts about my journey into this awesome language and ecosystem'
categories:
  - blog
tags:
  - scala
header:
  image: '/images/header.jpg'
---

## Intro

It's been more than a two years since I started learning `Scala`. So, I think it's a good time to review my journey and share my thoughts about this awesome language and its ecosystem.

## My journey

My first contact with `Scala` was through the book `Functional Programming in Scala` (aka `The Red Book`). With that book I learned some functional programming ideas and basic `Scala` syntax. I must confess that reading it was very challenging for me. The exercises were difficult and makes you think over them for a while until you came up with the solution (If you could).

However, at some point of the reading I thought that It was over complicated and I started considering learning from another resource. So, I came out with some Akka related Udemy courses. I had a great experience learning Akka basics, some Akka Persistence stuff, but learning Akka Streams simply blowed my mind. Writing async pipelines were awesome and I felt in love with Stream processing, in general, and Akka Streams and Alpakka, in particular.

After that, I got my first job as a `Scala` developer, and I started learning more about `Play Framework` and I had my first contact with `Typelevel libraries`, such as `Cats` and `Circe`.

In my free time I continued reading some books. I read `Scala Essentials`, `Scala with Cats` and I've tried to develop some applications using just the `Typelevel stack` (`Cats`, `Http4s`, `Doobie`, etc) but I've never succeeded. Until now I still think that `Typelevel` ecosystem is very challenging and over complicated. I will not deny that the way of reasoning about effects and developing an app just with pure functions is very interesting and that has its benefits (for example, in testing), but I think the learning curve is very sharp and I don't know if it's really worth it. However, something is true: I'll continue playing around with it. I know that at some point it will make more sense to me.

Enough talking about myself, let's review some points about the `Scala` language and it's ecosystem.

## Language

`TODO immutability`

`TODO lazy vals`

`TODO case classes`

`TODO pattern matching`

`TODO functions as values`

`TODO type alias`

`TODO companion objects`

`TODO traits`

`TODO implicits`

`TODO the collections API`

`TODO monads`

`TODO for comprehensions`

`TODO async with Futures`

`TODO Typeclasses`

`TODO IO`

`TODO Akka`

The idea when creating `Scala` was to be a mix between `OOP` and `functional programming`, taking the best of both worlds and taking advantage of the rich set of libraries and frameworks of the `JVM` ecosystem. I think this goal was achieved. The language is very flexible and elegant.

## Community

Having a language that is so flexible comes with different approaches to solve the same problem. Because of that, different `schools` have been emerged in the `Scala` ecosystem.

At a first sight I could identify four `Scala` schools: the Odersky's one, Typelevel's one, ZIO's one and Lil Haoyi's one. Let's review each of them.

### The Odersky's school or Lightbend approach

Martin Odersky is the creator of the `Scala` language, and this school follows his idea: having a mix between `OOP` and `Functional Programming`. I think his vision was achieved and the language itself is awesome. Things like inmutability, lazy evaluation, case classes, traits, implicits (without abusing of them), the collections API, monads and for comprehensions, and Futures are simply great and you can solve a wide number of problems in a very elegant way using them. Additionally, you have Akka and Play, which gives you more tools for implement high performance services and introduce you to different concurrency models (CPS, Actors, Streaming, Distributed programming).

I think diving into this approach is the easiest way to learn `Scala`. Also, learning the `Lightbend stack` (`Akka`, `Akka Cluster`, `Akka Streams`, etc) gives you lots of insights about architecture and design. Because of that, it is not strange that [big companies](https://www.lightbend.com/case-studies), such as `Pay Pal`, `Wallmart`, `MOIA`, `Tesla`, `Tubi` or even [Fortnite](https://www.lightbend.com/blog/akka-powers-400-million-users-streaming-gaming-entertainment) choose those technologies.

However, given the flexibility of `Scala` plus the rich and solid `JVM` ecosystem, new approaches has been emerged. Many of them are very influenced by `Haskell` and try to implement pure functional programming on the `JVM`.

### Typelevel school

[Typelevel](https://typelevel.org/) takes ideas from `Haskell` and `Type safety` but in another level and you can see that influences in their core libraries (`Cats` and `Cats Effect`), and also in libraries that are based on them, such as `Doobie`, `Http4s` and `Circe`. Those libraries are very concerned about how to deal with `side effects` in a safer way, and also, they are very concerned about `Type Safety` (but sometimes, too much concerced from my point of view). I must confess this approach is very interesting. However, whenever I try to learn it I always end up frustrated in some way. For example, let's see the following code:

``` scala
def allRoles[F[_], Auth](
    pf: PartialFunction[SecuredRequest[F, User, AugmentedJWT[Auth, Long]], F[Response[F]]],
)(implicit F: MonadError[F, Throwable]): TSecAuthService[User, AugmentedJWT[Auth, Long], F] =
  TSecAuthService.withAuthorization(_allRoles[F, AugmentedJWT[Auth, Long]])(pf)
```

I think the approach of abstracting over the effect system and define interpreters is cool, but trying to be ultra type safe is not beginner friendly and engornomic (IMO).

Typelevel has a lot of resources to learn and its community is very active. Reading its docummentation is always enriching.

### ZIO

The [ZIO](https://zio.dev/) community is reaching lot of traction during the last couple of years. `ZIO` is an effect system for asynchronous computations, whose approach is to use idiomatic `Scala` code without abusing of implicits and type classes. Its entry barrier is very low compared with the `Cats` one. Also, you can see a `ZIO` as a `Future` with steroids.

Its community is very active and charmfull. The ZIO vision is to provide libraries for writting `Pure Functional` and concurrent programs using idiomatic `Scala`. I hope I will try ZIO this year.

Nowadays, I'm not considering using `ZIO` for full backend development as I'll do with `Typelevel` or `Play/Akka/Future`, because I consider it too much low level sometimes. But that's just my noob opinion. However, I think it is a great option for building tools, such as [Greyhound](https://github.com/wix/greyhound) or [Caliban](https://github.com/ghostdogpr/caliban).

### The Python approach

Another `Scala` school is the [Li Haoyi's one](https://www.lihaoyi.com/). He took some ideas from `Python` and translated them to the `Scala` world. You can see those ideas in the [ecosystem of libraries and tools](https://github.com/com-lihaoyi) that he created, which are focused on usability and efficiency. Its libraries are beginner friendly and really useful. I really want to experiment with them and also give to [Ammonite](https://github.com/com-lihaoyi/Ammonite).

You can also read [his book](https://www.handsonscala.com/) and he also has an [interesting talk](https://www.youtube.com/watch?v=UiN6yZPAYww&t=454s) about how `Scala` development is approached at `Databricks` (the company he works in).

Summarizing, I think that having different ways of thinking is also a bless because it gives you different tools and approaches for solving problems, but it very important to define some guidelines or standarization for styling when working on a big team or a big codebase. Also, I feel that pragmatism and cleanest are the two main concerns that every developer should have in mind when developing software.

## Tooling

`TODO sbt`

`TODO sbt plugins`

`TODO intellij`

`TODO metals`

## Frameworks and Libraries

`TODO Cats/Cats Effect`

`TODO ZIO`

`TODO Akka`

`TODO Play Framework`

`TODO Spark`

## Next steps in my journey

Nowadays, I'm enjoying so much doing katas in `Scala`, using `tail recursion` and trying to find my way implementing immutable datastructures and the traditional ones. Also, I'm trying to learn some `Akka Cluster` stuff through the new [Akka Typed API](https://doc.akka.io/docs/akka/current/typed/actors.html)
and reading/experimenting with the [IO monad from Cats Effect](https://typelevel.org/cats-effect/docs/concepts) (in spite of not being a fan of `Tagless Final`, I really love the IO monads from Cats and really want to know more about it).

Also, I always try to watch conferences and beign up to date with the community on their different channels.

In my roadmap, there are more things to learn: such as [Scala 3](https://docs.scala-lang.org/scala3/new-in-scala3.html), [Ammonite](https://ammonite.io/), [Scalajs](https://www.scala-js.org/) and some [Spark](https://spark.apache.org/docs/latest/quick-start.html).

## Conclusions

Scala is a great language with a great ecosystem. There are lots of smart people to learn from. So, If you are a Java developer and you want to grow, I strongly recommend you to give it a try. All the things that I learned during this journey made me growth as a developer .....................