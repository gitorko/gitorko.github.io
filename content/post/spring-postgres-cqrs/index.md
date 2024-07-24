---
title: 'Spring Boot Postgres - CQRS (Multiple Database)'
description: 'Spring Boot Postgres - CQRS (Multiple Database)'
summary: 'Spring Boot Postgres - CQRS (Multiple Database)'
date: '2024-06-27'
aliases: [/alias/]
author: 'Arjun Surendra'
categories: [Spring, JPA, Liquibase, CQRS]
tags: [jdbc, webflux, cqrs, multi-database, liquibase, leader-follower]
toc: true
draft: false
---

Spring boot implementation of CQRS pattern

Github: [https://github.com/gitorko/project99](https://github.com/gitorko/project99)

## Main Topic

CQRS (Command and Query Responsibility Segregation) a pattern that separates read and update operations for different data store. This maximizes application performance, scalability, and security.
We will start 2 database servers where writes goto the primary database and reads are done on the secondary database. Replication happens from primary db to secondary db.
This is an AP model (CAP Theorem) as replication will result in eventual consistency.

![](cqrs-postgres.png)

### Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project99/main/src/main/java/com/demo/project99/config/PrimaryDataSourceConfig.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project99/main/src/main/java/com/demo/project99/config/SecondaryDataSourceConfig.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project99/main/src/main/java/com/demo/project99/service/EmployeeService.java" >}}

### Postman

Import the postman collection to postman

[Postman Collection](https://raw.githubusercontent.com/gitorko/project99/main/postman/Project99.postman_collection.json)

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project99/main/README.md" >}}

## References

[https://spring.io](https://spring.io/)
