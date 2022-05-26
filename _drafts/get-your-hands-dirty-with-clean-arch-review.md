---
title: Hexagonal Architecture
excerpt: 'TODO TODO TODO TODO TODO'
categories:
  - blog
tags:
  - review
header:
  image: '/images/header.jpg'
---

# Intro

In this review we are going to talk about the awesome book [Get Your Hands Dirty with Clean Architecture](https://reflectoring.io/book/) by `Tom Hombergs`

# Who is audience of this book?

I think this book is great for any software developer, architect or tech lead involved in the creation of software which handles complex business rules wich a high expectation of change and adaptability over the time.

# The author

`TODO`

# Review

The books starts explaining the disadvantages of the traditional and well known `Layered Architecture`. It explains how this architecture is persistence oriented, how easy is to mix layers and loose discipline and how prone to mantainability issues is.

Then, the author explains why `Single Resposibility Principle` and the `Dependency Inversion Principle` (yes, the `S` and `D` you know from `SOLID`) are important in our jorney towards `Hexagonal Architecture`. Also, he explains `package organization`, which is something really important not just to organize our code, but to express our architecture intentions.

Then, the practical part starts by implementing a `Use Case`. We start by the domain and then moving to the differents adapters we need, using the different ports and giving great tips in the middle. For example: one tip that blows my mind was to keep validation in the `application layer` though `command objects` (it is a way to have the validation explicitly in the model), also, the explanation of why having validation in the `application layer` is awesome. This technique is also known as `input model`.

*** otro tip del autor: qué debe retornar un use case luego de ejecutarse? recordar, un use case puede ser accedido por diferentes ports. Entonces, conviene retornar algo que sea bien significativo del use case (siendo agnostico del adapter), o sino, otro approach es retornar la cantidad mínima de info que sea significativa, y de ultima se crea otro use case cuya función sea acceder a cierta data (TODO mejorar esta explicación).

# How to take the most of this book

As I said before, the book has a really practical approach. I recommend coding the `buckpal` application together with the author. Also, I suggest to take a pet project you have and refactoring it using the ideas exposed in this book. By following those tips, you'll find yourself adopting this approach very quickly and getting the benefits of this interesting architecture.

As a theoric complement, I strongly recommend watching the talk [Clean Architecture with Spring](https://www.youtube.com/watch?v=cPH5AiqLQTo) by the same author, and reading the post [Hexagonal Architecture](https://alistair.cockburn.us/hexagonal-architecture/) by `Alistair Cockburn`, the creator of this architectural style.

# Conclusion

`TODO`