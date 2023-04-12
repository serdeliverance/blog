---
title: Context Bounds
excerpt: 'Reviewing context bounds'
categories:
  - blog
tags:
  - scala
  - scala 3
header:
  image: '/images/header.jpg'
---

A context bound is a syntactic sugar for expressing a `context parameter` that depends of a `type parameter`. For example, imagine that we have the following method:

``` scala
def maximum[T](xs: List[T])(using Ordering[T]): T =
  xs.reduceLeft(max)
```

Note: in the previous example, `max` is a function that has the following signature: `def max[T](x: T, y: T)(using ord: Ordering[T])`.

This can be rewritten using `Context bounds`:

``` scala
def maximum[T: Ordering](xs: List[T]): T =
  xs.reduceLeft(max)
```

`: Ordering` is the bound and it indicates that we have a `context parameter` of type `Ordering[T]`. So, it is equivalent to state that we have `(using Ordering[T])` as parameter.

# Conclusion

In this short post we've seen `Context Bounds` which is an interesting way of expressing `context parameters` that relies on `type parameters`. This syntax is very useful when writing `type classes` or for expressing `effect systems` capabilities.
