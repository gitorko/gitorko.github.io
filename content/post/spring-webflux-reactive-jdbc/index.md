---
title: 'Spring Webflux & Reactive JDBC'
description: 'Spring Webflux & Reactive JDBC'
summary: 'Webflux integration with reactive JDBC, to allow non-blocking calls to database.'
date: '2019-04-03'
aliases: [/spring-webflux-reactive-jdbc/]
author: 'Arjun Surendra'
categories: [Spring, JPA]
tags: [reactive-jdbc, webflux]
toc: true
---

Webflux integration with reactive JDBC, to allow non-blocking calls to database.
R2DBC is still not recommended for production, hence this approach should help you integrate existing relational database with webflux.

Github: [https://github.com/gitorko/project64](https://github.com/gitorko/project64)

## Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project64/main/src/main/java/com/demo/project64/Main.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project64/main/src/main/java/com/demo/project64/service/AbstractReactiveService.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project64/main/src/main/java/com/demo/project64/service/CustomerReactiveService.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project64/main/src/main/java/com/demo/project64/repository/CustomerRepository.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project64/main/src/main/java/com/demo/project64/controller/HomeController.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project64/main/src/main/java/com/demo/project64/config/SchedulerConfig.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project64/main/src/main/resources/application.yaml" >}}

## Setup

{{< markcode "https://raw.githubusercontent.com/gitorko/project64/main/README.md" >}}

## Postman

![](img01.png)

Import the postman collection to postman

[Postman Collection](https://raw.githubusercontent.com/gitorko/project64/main/postman/Project64.postman_collection.json)

## References

[https://spring.io/blog/2018/12/07/reactive-programming-and-relational-databases](https://spring.io/blog/2018/12/07/reactive-programming-and-relational-databases)
