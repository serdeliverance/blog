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

This app is a minimalistic/pet project `crypto wallet` app. Actually, `crypto wallet` is its name. It offers the following operations:

- managing users (CRUD operations, uses for admins)
- buy, sell and transfer crypto currencies
- allow users to see their portfolio balance (the quotation of their cryptocurrencies portfolio converted to USD)
- allows users to see their transaction history

It was written using a traditional `N-Layer` style with `Java` and `Spring Boot`. Following is an overview of the package structure:

![initial-package-structure]({{ site.baseurl }}/assets/images/migrate-nlayer-app-to-hexagonal/initial-package-structure.png "Initial Package Structure")

This project already has `Kotlin support` configured. However, if you want to learn how to add kotlin support to your existing `Spring Boot` project, you can check this [previous post](https://serdeliverance.github.io/blog/blog/add-kotlin-to-your-spring-boot/)

Let's move forward and start refactoring.

# Selecting our first use case

I think starting simple is better. This will be an incremental process. To do so, I suggest start for a very simple `use case`. In this case, `Get user by id` seems to be a good candidate. The logic that implements this use case is distributed in the following classes: `User`, `UserController`, `UserService` and `UserRepository`.

# Fat services

Let's take a minute to check the `UserService` before talking about the `strategy we are going to use`:

``` Java
@Service
@RequiredArgsConstructor
@Slf4j
public class UserService {

    private final UserRepository userRepository;

    public Optional<User> get(Integer id) {
        log.info("Getting user with id: {}", id);
        return userRepository.find(id);
    }

    public List<User> getAll() {
        log.info("Getting all users");
        return userRepository.getAll();
    }

    public void create(User user) {
        log.info("Creating user");
        userRepository.save(user);
    }

    public void update(User user) {
        Integer userId = user.getId().get();
        if (this.exists(userId)) {
            userRepository.update(user);
        } else throw new ResourceNotFoundException("user: " + userId);
    }

    // More and more stuff reduced for brevity
```

As you can see, this service has a lot of logic. At a first sight, you can image that more than one use case have some logic inside this class. This kind of anti-pattern is called `Fat Service` and it is something we want to avoid in `Hexagonal Architecture`. It is a service that can be involved in lots of use cases, making difficult to test, mantain and also to parallelize work.

We are going to try to decompose this class (and others like this) gradually until being finally removed.

# The strategy

I think a good approach for doing this is to use something similar to the well known [Strangler Pattern](https://microservices.io/patterns/refactoring/strangler-application.html) from `Microservices`.

`TODO brief explanation of strangler patter`

 We are going to migrate this functionallity using `Hexagonal Architecture` but without erasing the original code. So, in some way, we are going to mantain two versions of the same functionallity during the migration (as `Strangle Pattern` suggest). After