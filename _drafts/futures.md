---
title: Futures 101
date: 2021-11-24 09:00:00
excerpt: 'An intro to Future and how to use it in the real world'
categories:
  - blog
tags:
  - scala
header:
  image: '/images/header.jpg'
---

# Intro

# Use case

``` scala
def processPayment(payment: Payment) = {
  // 1. send to credit card
  // 2. store transaction result
  // 3. adding billing
}
```

# Asynchrony

`TODO definition of asynchrony`

# Concurrency vs Parallelism

`TODO definition`

# Traditional sync/block mechanism


There are also useful for firing background tasks:

``` scala
// TODO background task example
```

Disadvantages:

- very low level primitive
- not composable
- tend to be used with shared memory, so race conditions can arise
- because of the previous one, also deadlocks are more likely to happend

# Another approach for implementing concurrency

`TODO where future come from?`

# Execution context

# Future

# API and basic operations

# Sequencing computations

# Error handling (Future recover and recoverWith)

The same example as the beginning, but adding recover with on billing callback if it fails:

``` scala
def processPayment(payment: Payment) = {
  // 1. send to credit card
  // 2. store transaction result
  // 3. adding billing => WITH RECOVER!! (it should update the transaction)
}
```

# Refactoring our code using Futures

# Extending future

`TODO retry`

`TODO traversal`

# Testing Future

# The ugly of Future (an intro)

- exception based API
- also, modeling errors through exceptions is not descriptive enough (we could implement a hierarchy of custom exceptions but It is not a functional programming approach)
- Futures are eager evaluated, when declared, the async computation is already schedule and fired. So, they are not referential transparent
- Not important now, but a Future has a correspondence with a SO thread.

# How to solve it

The way futures work at low level (SO threads for example) is something that is out of our control. However, we can improve the way we work with Future at a Higher Level/End user API using some functional programming ideas.

## EitherT

`TODO`

Advantages:

- rich error semantics: we model errors as values using ADT which give us more detail about the error
- sequencing and shortcircuiting
- just sequence operations, let frameworks to the rest.

Disadvantages:

- run parallel task must require to fire a future from another

# Best practices recommendations

* Use `Future` when it is really needed. In other words: if you have all the data you need in memory (ex: `2 + 2` or reducing a list), don't use `Future`
* Use `Future.flatMap` if it is really needed. Similar to the previous one. When you create a `Future`, you are scheduling a computation task in the operative system and its not cheap. So, if you need to chain a future result to another operation that does not require interacting with an external system and could be managed in memory, use `map` instead. Example

``` scala
getAllPayments()  // you go to database to retrieve all payments. So, it returns a Future[List[Payment]]
  .map(payments => payments.sum) // you have the List in memory at this point, you not require a future, just map and operate with the list
```

* Don't use `Thread.sleep` (I know, it is not specific from `Future`, but it is important to be mentioned). A `Thread` is a valuable resource and when you do this, what you are doing is blocking a `Thread`, leaving it unavailable for doing some meaningful processing (the `Thread` is doing nothing during this time, and it could be doing something more useful)

* Don't use `await` unless it is really needed. The same as the previous point, you are blocking a thread (in this case, the current one) until the computation is completed.

* Avoid using `onComplete`: 99 percent of the times with `map` and `flatMap` you would be Ok. You won't be required to use `onComplete` callbacks