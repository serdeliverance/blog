---
title: Defensive Programming
excerpt: 'General tips for writing safe, resilient and self explanatory code'
categories:
  - blog
tags:
  - clean code
header:
  image: '/images/header.jpg'
---

# Intro

# Avoid Null Values

- return empty object (aka `Null Object Pattern`). It simplifies client code because they won't need to write additional code for handling null or invalid values.

- use built-in types, such as `Optional` (in `Java`)

# Use immutable variables

Immutable variables prevent unexpected modification. If your language support them, please use it.

Another advantage is that it makes simpler to use parallel programming. Also, compiled languages can make optimizations if they know that a variable is immutable.

# Use Types

Having your variables strictly typed gives you extra level as you can catch more errors at compile time. So, if you are using `Javascript`, given `Typescript` a try is a wise decision.

# Validate your inputs

- validate your inputs using preconditions and post conditions. It goes in hand with the `Fail Fast` principle.
- use libraries and frameworks for input validation. For example: if you are using Java/Spring Boot, use `jakarta.Validation` api for validating your POJOs.
- validate input first. So, if you are writing a method, the first lines of such a method should be input validation. That guarantees that further processing will be working with valid data.

# Make your methods signature the source of truth

- Leverage exceptions when needed and built-in types when possible by letting them make your methods and APIs explicit. Let your methods explicitly state what is the expected input and possible output. I mean, no hidden RuntimeExceptions that are invisible at method signature and you just know about that existence when running the app on Prod. Also, if you rely on them, try to write explanatory documentation so API users will have it into account at the moment they use your functionality.

# Exceptions

- don't use special return types (such as `null`, `-1`). Use exceptions because they can give you extra info (such as `Stack Trace`). Also, you can use built-in types when possible.
- Don't use exceptions for modeling alternative paths on business logic.
- Use `Exceptions` when you are really dealing with an error or an exception case.
- Don't use generic exceptions as they don't give enough info the client.
- Use built-in exceptions as possible. Those are well know in the community and also simplify onboarding because they are standard.

# Throw exceptions early, catch Exceptions late

Throwing exceptions early means that the throwing of the Exception must happen immediately after the exceptional error arises, not later. It makes the exception code easy to identify and track.

Catch the exception late means that you should catch the exception in the layer that makes sense to do so. I mean, the layer that can take further actions for mitigation, retrying or executing alternative logic based on this condition. In this case, intermediate layers should propagate the exception to upper layers and avoid unnecessary catching logic that can `swallowing` your exception and make it being missed or lost info when arriving to the part of the system that should take care of it.

We can see this principle very often when following Functional Programming principles:

`TODO example with Scala`

Another very important tip: when you have an exception, log it.

# Retry Smartly

Not retry everything, just do it when you need guarantee some writing operation is performed or when some process needs to be executed.

Use backoff strategy with some jitter in such a case.

# Fail Fast and loudly

`Fail Fast`: let the application crash if it encounters and exception that is not able to handle. Fail fast and loudly (log relevant information).

# Build Idempotent systems

# Logging
