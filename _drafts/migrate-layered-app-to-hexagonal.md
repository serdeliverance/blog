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

## ----------------------------------------------------
# POST HEXAGONAL ARCHITECTURE
## ----------------------------------------------------

# Intro

# Layered architecture

`TODO brief explanation`

`TODO a brief diagram`

# Disadvantages or Layered architecture

- la capa de persistencia tiene más importancia (`data driven`). Esto se refleja en que el desarrollo siempre empieza por dicha capa y luego se pasa a las siguientes. Con los ORM y frameworks de la capa de acceso a datos, todavía toma mas predominancia y se genera un fuerte acoplamiento.
- al desarrollar un sistema, generamos un modelo que representa el negocio y que expone funcionalidades a los usuarios. Perdemos esta capacidad dado que estamos dando más importancia al `estado` (como se ha mencionado en el punto anterior, por enfatizar la capa de persistencia), que al `comportamiento` (es decir, al dominio). El dominio pierde protagonismo y, lo que es peor, el dominio depende fuertemente/está fuertemente acoplado a la capa de persistencia.
- es facil tomar `shortcuts`, y empezar a mixear código de diferentes capas (por ejemplo, hacer que la capa web referencie directamente a la capa de persistencia). Se requiere disciplina en el equipo para evitar las `Broken Windows`.
- es dificil paralelizar (debido a que el desarrollo empieza desde la capa de persistencia y, hasta no tenerlo hecho, no se puede avanzar hacia las capas mas externas)
- es dificil identificar los `use case` en el código, dado que tenemos `fat services` (ej: `UserService`, por si a alguno le suena), es decir servicios que involucran una rebanada de varios casos de uso. Sumado a eso, gran parte del código se encuentra en la persistencia, y eso si no empezamos a mixear capas.

En aplicaciones `CRUD` puede ser una buena opción. No obstante, en aplicaciones que tengan muchas reglas de negocio y que tengan proyección de crecimiento, una arquitectura de capas tradicional no es la mejor opción para generar código mantenible y fácilmente adaptable a cambios.

# Some SOLID principles to the rescue

Para migrar a una arquitectura/organización de componentes más mantenible, podemos basarnos en dos sólidos principios de la `OOP`, o más precisamente, de `SOLID`:

- `Single Responsibility Principle`
- `Dependency Inversion`

# Single Resposibility Principle

El principio de `Single Responsibility` generalmente es interpretado como que un componente debe hacer una sola cosa y hacerla bien. No obstante, podría interpretarse como que `un componente debe tener un solo motivo para cambiar`. En la práctica, en sistemas muy mal arquitecturados, generalmente no vemos esto, y una modificación en un componente, generalmente desencadena cambio en quienes dependen de él. No obstante, si logramos conservar este principio, podemos hacer que los componentes sean más mantenibles, y que el impacto en componentes adyacentes no les afecte, o que dicho impacto sea mínimo. Esto, extrapolado a nivel arquitectura, nos permitiría tener componentes desacoplados y con una funcionalidad o scope bien definidos.

# Dependency Inversion Principle

> We can turn around (invert) the direction of any dependency with our codebase.
>
> -- <cite>Misterious Head of Wisdom</cite>

`TODO`

# Hexagonal Architecture

`TODO`

Es importante notar que este packaging es muy expresivo y hace muy facil identificar no solo los casos de uso, sino también los adapters. Esto ayuda mucho para saber dónde hay que trabajar o hacer foco al momento de desarrollar nuevos features (crear, modificar adapters, o implementar nuevos use cases)

`TODO imagen del packaging`

Este packaging es muy expresivo y hace la arquitectura más explicita para los developers.

## ----------------------------------------------------
# POST MIGRATING SPRING BOOT APP TO CLEAN ARCH - PART 1
## ----------------------------------------------------

# A brief overview about Hexagonal Architecture

`TODO hexagonal architecture explanation`

For a more detailed explanation about `Hexagonal Architecture` you can check my [previous post](https:www.todo.com).

# Our starting point

This app is a minimalistic/pet `crypto wallet` project. Actually, `crypto wallet` is its name. It offers the following operations:

- managing users (CRUD operations, uses for admins)
- buy, sell and transfer crypto currencies
- allow users to see their portfolio balance (the quotation of their cryptocurrencies portfolio converted to USD)
- allows users to see their transaction history

It was written using a traditional `N-Layer` style with `Java` and `Spring Boot`. Following is an overview of the package structure:

![initial-package-structure]({{ site.baseurl }}/assets/images/migrate-nlayer-app-to-hexagonal/initial-package-structure.png "Initial Package Structure")

Let's move forward and start refactoring.

# Selecting our first use case

I think starting simple is better. This will be an incremental process. To do so, I suggest start for a very simple `use case`. In this case, `Get user by id` seems to be a good candidate. The logic that implements this use case is distributed in the following classes: `User`, `UserController`, `UserService` and `UserRepository`.

# Fat services

Let's take a minute to check the `UserService` before talking about the strategy we are going to use:

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

As you can see, this service has a lot of logic. At a first sight, you can image that more than one use case have some logic inside this class. We can called this pattern as `Fat Service` and it is something we want to avoid in `Hexagonal Architecture`. It is a service that can be involved in lots of use cases, which make it difficult to identify them at first sight. This kind of services are difficult to test, mantain and also to parallelize work (lot of people working of different use cases are prone to be working on the same file).

We are going to decompose this class (and others like this) gradually until being finally removed.

# The strategy

I think a good approach for doing this is to use something similar to the well known [Strangler Pattern](https://microservices.io/patterns/refactoring/strangler-application.html) from `Microservices`.

`TODO brief explanation of strangler patter`

 We are going to migrate this functionallity using `Hexagonal Architecture` but without erasing the original code. So, in some way, we are going to mantain two versions of the same functionallity during the migration (as `Strangle Pattern` suggest). After

# Domain and Application Layer

Let's start by the inner layers of our architecture: `domain` and `application`.

`TODO image of domain and application`

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

We are going to apply a little refactor to make our class immutable, by using [@Value](https://projectlombok.org/features/Value) annotation from [Lombok](https://projectlombok.org/):

``` java
@Value
@AllArgsConstructor
public class User {

    Integer id;
    String username;
    String password;
    String email;
}
```

{{{{ MARKER: HERE LAST TIME}}}}

Also, we took the opportunity to make a light improvement: we defined the `user` in our domain to always have its id field defined (we removed the `Optional`).

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

Let's see how our packaging is taking shape:

![packaging-checkpoint-1]({{ site.baseurl }}/assets/images/migrate-nlayer-app-to-hexagonal/checkpoint-1-package-view.png "Checkpoint 1")

`TODO component diagram of the current architecture`

You can see the complete PR [here](https://www.todo.com)

# A more complicated use case: Money transfer

1) primero, migrar el domain

``` kotlin
data class Transaction(
    val id: Long,
    val userId: Int,
    val cryptoCurrencyId: Int,
    val amount: BigDecimal,
    val operationType: OperationType,
    val transactionDate: LocalDateTime
)
```

2) definir el usecase

en application.port.in creamos nuestro use case:

``` kotlin
interface TransferMoneyUseCase {
    fun transfer(issuer: User, receiver: User, cryptocurrency: Cryptocurrency, amount: BigDecimal): Unit
}
```

Nuestra implementacion va a ser muy simple. Si la transferencia es exitosa, retorna Unit, caso contrario fallará con alguna excepcion

Disclaimer: sé que para algún aficionado de la programación funcional esta effectful implementation no es muy atractiva (recordar que nuestro foco aquí es la implementación de arquitectura hexagonal) .No obstante, un tema interesante para un próximo artículo podría ser refactorizar esta app para darle un toque más funcional.

3) en application.service implementamos dicha funcionalidad

``` kotlin
class TransferMoneyService : TransferMoneyUseCase {
    
    override fun transfer(issuer: User, receiver: User, cryptocurrency: Cryptocurrency, amount: BigDecimal) {
        TODO("Not yet implemented")
    }
}
```

Las operaciones que queremos hacer acá son:

1. cargar los balances de cada usuario
2. validar que el issuer tiene fondos suficientes para hacer la transferencia
3. extraer el monto de la cuenta de origen
4. depositar el monto en la cuenta de destino

Con esto, podemos notar que minimamente necesitamos un colaborador para la carga de balances. Entonces, se podria decir que necesitamos un ouput port, o un application driven port. Es una buena practica definir los puertos de forma que su nombre sea expresivo de la funcionalidad que representa (esto también hace que sean más granulares). Entonces, empezamos definando dicho colaborador:

``` kotlin
class TransferMoneyService(val loadBalancePort: LoadBalancePort) : TransferMoneyUseCase {

    override fun transfer(issuer: User, receiver: User, cryptocurrency: Cryptocurrency, amount: BigDecimal) {
        TODO("Not yet implemented")
    }
}
```

Dejemos creado dicho colaborador en el package application.port.in:

``` kotlin
interface LoadBalancePort {

    fun getBalanceByCurrency(userId: Int, currencyId: Int): Balance
}
```

No tenemos definido una clase Balance, entonces creemosla:

``` kotlin
data class Balance(val userId: Int, val cryptocurrency: Cryptocurrency, val amount: BigDecimal)
```

Ahora, nuestra implementación será la siguiente:

``` kotlin
class TransferMoneyService(val loadBalancePort: LoadBalancePort) : TransferMoneyUseCase {

    override fun transfer(issuer: User, receiver: User, cryptocurrency: Cryptocurrency, amount: BigDecimal) {
        val issuerBalance = loadBalancePort.getBalanceByCurrency(issuer.id, cryptocurrency.id)
        if (issuerBalance.amount >= amount) {
            // debit amount from issuer
            // deposit amount to receiver
        }
    }
}
```

Podemos ser mas espeficios y tener un use case para el deposito, y otro para la extracción:

``` kotlin
class TransferMoneyService(val loadBalancePort: LoadBalancePort, val withdrawalUserCase: WithDrawalUseCase, val depositUseCase: DepositUseCase) : TransferMoneyUseCase {
    // remaining code commented
}
```

Terminamos de implementar el use case, dejando que la lógica de negocio vaya revelando cuales métodos necesitamos de nuestros nuevos colaboradores:

``` kotlin
class TransferMoneyService(val loadBalancePort: LoadBalancePort, val withdrawalUserCase: WithDrawalUseCase, val depositUseCase: DepositUseCase) : TransferMoneyUseCase {

    override fun transfer(issuer: User, receiver: User, cryptocurrency: Cryptocurrency, amount: BigDecimal): Transference {
        val issuerBalance = loadBalancePort.getBalanceByCurrency(issuer.id, cryptocurrency.id)

        if (issuerBalance.amount < amount)
            throw InsufficientFundsException()

        val withdrawTransactionResult = withdrawalUserCase.withdraw(issuer.id, amount)
        val depositTransactionResult = depositUseCase.deposit(receiver.id, amount)

        return Transference(issuer.id, receiver.id, amount, withdrawTransactionResult.id, depositTransactionResult.id, depositTransactionResult.transactionDate)
    }
}
```

Ahora que sabemos qué necesitamos, agregamos dichos metodos en las interfaces correspondientes:

``` kotlin
interface WithDrawalUseCase {
    fun withdraw(id: Int, amount: BigDecimal): Transaction
}
```

``` kotlin
interface DepositUseCase {
    fun deposit(id: Int, amount: BigDecimal): Transaction
}
```

TODO explicacion del tipo de dato Transference

TODO explicacion de la creacion de la exception

Disclaimer: esta implementación es simple y no tenemos en cuenta cuestiones de transaccionalidad.

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