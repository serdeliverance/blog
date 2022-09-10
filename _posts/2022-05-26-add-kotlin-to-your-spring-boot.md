---
title: Add Kotlin support to Spring Boot app
date: 2022-05-27 19:28:00
excerpt: 'How to add Kotlin support to Spring Boot'
categories:
  - blog
tags:
  - kotlin
  - spring boot
  - spring
header:
  image: '/images/header.jpg'
---

# Intro

In this brief post, we are going to see how to add `Kotlin` on an existing [Spring Boot project](https://github.com/serdeliverance/crypto-wallet-kt).

# About the project and setting up

It is a traditional `Java Spring Boot` app built with `Maven`. Of course, lot of changes have been made since I wrote this article, so If you want try by yourself you can [fork the github project](https://github.com/serdeliverance/crypto-wallet-kt) and run the following command:

```
git checkout -b adding-kotlin 455ca7c57b6ed14c8b53344dae86fb7c6d7d1f98
```

It will check out a new `branch` (called, `adding-kotlin`) from the specified revision (of course, at that point the project hadn't `Kotlin`).

Now, we are ready to continue

# Adding Kotlin support

Let's see our `pom.xml` and add `kotlin.version` property:

```
    <properties>
        <java.version>11</java.version>
        <kotlin.version>1.6.21</kotlin.version>
    </properties>
```

Now, we have to add the `kotlin-reflect` and `kotlin-stdlib-jdk8` dependencies in the `dependencies` section:

```
    <dependencies>
        <!-- other dependencies above -->
  <dependency>
   <groupId>org.jetbrains.kotlin</groupId>
   <artifactId>kotlin-reflect</artifactId>
  </dependency>

  <dependency>
   <groupId>org.jetbrains.kotlin</groupId>
   <artifactId>kotlin-stdlib-jdk8</artifactId>
  </dependency>
        <!-- more dependencies bellow-->
    </dependencies>
```

Then, in the `build` section, we need to add the source and test directories

```
    <build>
  <sourceDirectory>${project.basedir}/src/main/kotlin</sourceDirectory>
  <testSourceDirectory>${project.basedir}/src/test/kotlin</testSourceDirectory>
        <!-- more configs-->
    </build>
```

Of course, you need to create both directories in your project:

```
mkdir -p src/main/kotlin
mkdir -p src/test/kotlin
```

After that, in the `plugin` section, you need to add the `kotlin-maven-plugin` in order to configure the `kotlin compiler`:

```
<plugins>
    <plugin>
        <groupId>org.jetbrains.kotlin</groupId>
        <artifactId>kotlin-maven-plugin</artifactId>
        <configuration>
            <args>
                <arg>-Xjsr305=strict</arg>
            </args>
            <compilerPlugins>
                <plugin>spring</plugin>
                <plugin>jpa</plugin>
            </compilerPlugins>
        </configuration>
        <dependencies>
            <dependency>
                <groupId>org.jetbrains.kotlin</groupId>
                <artifactId>kotlin-maven-allopen</artifactId>
                <version>${kotlin.version}</version>
            </dependency>
            <dependency>
                <groupId>org.jetbrains.kotlin</groupId>
                <artifactId>kotlin-maven-noarg</artifactId>
                <version>${kotlin.version}</version>
            </dependency>
        </dependencies>
    </plugin>
</plugins>
```

The plugins `spring` and `jpa` that were added in the `compilerPlugins` section, are needed in order to make some magic in the byte code generation to be suitable for the specified java frameworks/libraries. For example, classes are final in kotlin by default and hibernate expected them to not be final. Another example is to generate the `no args constructor` on entity classes.

Now, we can create a `RestController` using `Kotlin` and compile the project to see if it is working as expected.

``` kotlin
// in src/main/kotlin
import org.springframework.web.bind.annotation.GetMapping
import org.springframework.web.bind.annotation.RestController

@RestController("/foo")
class FooController {

    @GetMapping
    fun foo(): String {
        return "foo"
    }
} 
```

# Conclusion

This was a brief tutorial about adding kotlin support to a `Spring Boot` application. You can also check the [specific commit](https://github.com/serdeliverance/crypto-wallet-kt/commit/71063715be072598adf54023e7299f1a99b2dbbf) to see the changes in more detail.
