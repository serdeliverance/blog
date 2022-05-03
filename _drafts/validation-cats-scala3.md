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

For this example, we are going to use the following entity:

``` scala
case class Account(
    name: String,
    description: Option[String],
    userId: Long,
    initialAmount: BigDecimal,
    createdAt: OffsetDateTime,
    id: Option[Long]
)
```

An `account` to be valid must satisfy the following rules:

- `userId` must be positive
- `initialAmount` must be positive
- `userId` must be positive
- `createdAt` must not be more than current time


# A quick intro to Validation

# Validated

# Conclusions