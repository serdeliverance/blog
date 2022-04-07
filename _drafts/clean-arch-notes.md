---
title: Clean Architecture notes
excerpt: 'Notes taken from reading Get Your Hands Dirty with Clean Architecture by Tom Hombergs'
categories:
  - blog
tags:
  - design
header:
  image: '/images/header.jpg'
---

## Intro

## Traditional N-Tier Architecure

Problems:

- is persistence layer oriented: we start with the persistence and then modeling the rest. Strong coupling with the persistence layer. Get worst when adyacent layers start coupling with more components of the persistence layer (business logic, references db entities). It would be more natural if we start from business logic instead.

- it is difficult to parallelize work: we cannot destinate one dev to the web layer, another for the business one and another for the persistence. The coupling with persistence layer is so strong that implies that this layer has to be finished in order to start working with the more external ones, so parallelism is not possible. So it is difficult to work on different this in parallel.

- shorcuts and broken windows: its is easier to reference persistence components from the web layer and when you start doing that is a one way travel.

## Importante Design principles from OOP

- SRP: the `Single Resposibility Principle`. We use to associate this principle with the following sentence: `SRP is when every component has just one reposibility`. However, it is crucial to interpret this principle in a different way: `SRP means that a component just have one reason to change`

- Dependency Inversion: it is the mechanisim that allow us to change de dependency direction. So, using it properly, we can make our domain infrastructure agnostic, because the dependecy direction comes from outside to the inner layers (domain) of our app.

## Inverting Dependencies

## Different layers

## Application Example

## Conclusions