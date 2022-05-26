---
title: Migrate a traditional Java N-Layer app to Hexagonal Architecture (and Kotlin too)
excerpt: 'Refactoring an app to hexagonal architecture'
categories:
  - blog
tags:
  - systems design
  - hexagonal architecture
  - java
  - kotlin
header:
  image: '/images/header.jpg'
---

# Intro

# A brief overview about Hexagonal Architecture

`TODO`

# Our starting point

This app is a minimalistic/pet project `crypto wallet` app. Actually, `crypto wallet` is its name). It offers the following operations:

- managing users (CRUD operations, uses for admins)
- buy, sell and transfer crypto currencies
- allow users to see their portfolio balance (the quotation of their cryptocurrencies portfolio converted to USD)
- allows users to see their transaction history

It was written using a traditional `N-Layer` style with `Java` and `Spring Boot`