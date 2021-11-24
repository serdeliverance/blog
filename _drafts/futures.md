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

# Values and Computations

In Scala (and also in Functional Programming languages) what we usually do is evaluating expressions:

``` scala
val result = 3 + 3
```

or some more elaborated expressions:

``` scala
val total = products.sum(_.price)
```

And it is ok. All of this operations happend in memory and the processor can deal with them almost instantly because all the info it requires this computation is in memory. However, imagine you are building the checkout of an e-commerce system. So, we have the shopping cart and we need to implement the following flow:


``` scala
def checkout(userId: Int, cardData: CardData) = {
  // 1. validate credit card data
  // 2. retrieve the user's shopping cart from redis
  // 3. validate shopping cart data
  // 4. send payment info to credit card company to authorize the payment
  // 5. store the transaction result in your data base
  // 6. return response to the user
}
```

# Traditional sync/block mechanism


There are also useful for firing background tasks:

``` scala
// TODO background task example
```

Disadvantages:

- very low level primitive
- not composable
- tend to be used with shared memory, so race conditions and deadlocks are more lickely to happend

# Future

# Where does Future comes from?

# Future under the hood

# API and basic operations

# Execution context

# The ugly of Future (an intro)

# How to solve it

# Adding more useful methods

# More extentions on Future

# Best practices recommendations

* Use `Future` when it is really needed. In other words: if you have all the data you need in memory (ex: `2 + 2` or reducing a list), don't use `Future`
* Use `Future.flatMap` if it is really needed. Similar to the previous one. When you create a `Future`, you are scheduling a computation task in the operative system and its not cheap. So, if you need to chain a future result to another operation that does not require interacting with an external system and could be managed in memory, use `map` instead. Example

``` scala
getAllPayments()  // you go to database to retrieve all payments. So, it returns a Future[List[Payment]]
  .map(payments => payments.sum) // you have the List in memory at this point, you not require a future, just map and operate with the list
```

* Don't use `Thread.sleep` (I know, it is not specific from `Future`, but it is important to be mentioned). A `Thread` is a valuable resource and when you do this, what you are doing is blocking a `Thread`, leaving it unavailable for doing some meaningful processing (the `Thread` is doing nothing during this time, and it could be doing something more useful)

* Don't use `await` unless it is really needed. The same as the previous point, you are blocking a thread (in this case, the current one) until the computation is completed.

* Avoid using `onComplete`.... `TODO explain more`