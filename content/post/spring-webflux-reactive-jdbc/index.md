---
title: 'Spring Webflux & Reactive JDBC'
description: 'Spring Webflux & Reactive JDBC'
summary: 'Webflux integration with reactive JDBC, to allow non-blocking calls to database.'
date: '2024-01-30'
aliases: [/spring-webflux-reactive-jdbc/]
author: 'Arjun Surendra'
categories: [Spring, JPA]
tags: [reactive-jdbc, webflux]
toc: true
---

Webflux integration with reactive JDBC, to allow non-blocking calls to database.

Github: [https://github.com/gitorko/project64](https://github.com/gitorko/project64)

## Webflux JDBC

This approach provides alternate way to integrate existing relational database with webflux if the project is not ready to use R2DBC.

### Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project64/main/src/main/java/com/demo/project64/Main.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project64/main/src/main/java/com/demo/project64/repositoryservice/AbstractReactiveRepoService.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project64/main/src/main/java/com/demo/project64/repositoryservice/CustomerReactiveRepoService.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project64/main/src/main/java/com/demo/project64/repository/CustomerRepository.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project64/main/src/main/java/com/demo/project64/controller/HomeController.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project64/main/src/main/java/com/demo/project64/config/SchedulerConfig.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project64/main/src/main/resources/application.yaml" >}}

### Postman

![](img01.png)

Import the postman collection to postman

[Postman Collection](https://raw.githubusercontent.com/gitorko/project64/main/postman/Project64.postman_collection.json)

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project64/main/README.md" >}}

## References

[https://spring.io/blog/2018/12/07/reactive-programming-and-relational-databases](https://spring.io/blog/2018/12/07/reactive-programming-and-relational-databases)
