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

``` java
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

# Domain and Application Layer

Let's start by the inner layers of our architecture: `domain` and `application`.

Talking about the `domain`, the only class we really care about is the `User` one. If we take a look to our original `User` (in Java), it looks like this:

``` java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {

    private Optional<Integer> id;
    private String username;
    private String password;
    private String email;
}
```

We can translate this, to a [data class](https://kotlinlang.org/docs/data-classes.html) in `Kotlin`:

``` kotlin
data class User(val id: Int?, val username: String, val password: String, val email: String)
```

Using `data classes` also favors in terms of immutability (which is something good for concurrent apps). We also translate the type of the `id` attribute from `Optional<Int>` to a [nullable attribute](https://kotlinlang.org/docs/null-safety.html#nullable-types-and-non-null-types).

In order to not have collisions and also to avoid breaking working code, we define this data class in a new `package` called `v2.domain`. Once all `classes` in the original `domain` package were migrated, we can rename our `v2.domain` package to `domain`.

Now its time to define the `application` layer. In order to do that, let's create a `package` called `application`. Inside that, we have to define the ports with the functionallity we are going to expose to the `outside world` (`in ports`) and the functionallity we require from it (`out ports`).

As our `GetUserByIdUseCase` will be exposed to the outside world, we'll define it in the package `application.port.in`:

``` kotlin
interface GetUserByIdUseCase {
    fun getUserById(id: Int): User?
}
```

Defining an `interface` allow us to hide implementation details to the `adapter layer`.

We can now implement our `use case`. To be cleaner, we are going to implement it in a new `package` called `service`:

``` kotlin
@Service
class GetUserByIdService(private val userRepository: UserRepositoryPort) : GetUserByIdUseCase {

    override fun getUserById(id: Int): User? {
        return userRepository.find(id)
    }
}
```

As you can see, we require some functionally from the `outside world`. In this case, we require a `port` for retrieving the users.

We are going to define our `output port` and then implement it in the `adapter layer`.

# Persistence Adapter

`TODO`

# Web Adapter

`TODO explanation`

`TODO screenshot of package structure`

`TODO component diagram of the current architecture`

# A more complicated use case: Money transfer

# Domain and Application Layer

TODO mention anemic vs DDD modeling and why we choose to be anemyc in this implementation

# Web Adapter

# Persistence

# Testing

# Refactoring a previous use case

TODO refactor `getUserByIdUseCase` use to be a query instead

# Disclaimer

Throughout this tutorial we have seen how to create adapters, ports and use cases that can evolve independently. However, our code is not so `technology agnostic` as it could be, because we rely strongly in the `Spring Boot` platform for `dependecy injection` and `auto configuration`. We can see that our services and repositories, which are annotated with `@Service` and `@Repository` annotations. It is not wrong and also it was on purpose, because I really wanted to take advantage of these benefits. What I'm trying to say is that the overall architecture is what really matters. Having our domain centric and not dependant of any outer layer is crucial, and having the freedom to refactor our adapters, adding new ones easily, parallelizing work and having a visual architectural representation through packaging are benefits that have no price. There always will be architectural components or primitives that will take more or less protagonism. Remember: extreme purism is not practical. Bringing business value is what really matters.

Of course, there are approaches to develop a completely agnostic `Hexagonal Architecture` following a more minimalistic approach. Maybe, it can see a topic for a future post ;)

 # Conclusion