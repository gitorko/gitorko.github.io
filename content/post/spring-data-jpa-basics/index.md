---
title: 'Spring Data JPA - Basics'
description: 'Spring Data JPA - Basics'
summary: 'Spring Data JPA - Basics'
date: '2024-06-12'
aliases: [/spring-data-jpa-basics/]
author: 'Arjun Surendra'
categories: [Spring, JPA, Hibernate]
tags: [jdbc, webflux, onetomany, manytoone, onetoone, jointable]
toc: true
draft: false
---

Spring JPA examples on how to create domain clasess that map to database.

Github: [https://github.com/gitorko/project82](https://github.com/gitorko/project82)

## Spring Data JPA

Spring Data JPA provides an abstraction layer over the Java Persistence API (JPA)
Spring Data JPA offers a repository abstraction that allows developers to interact with their data models using a repository pattern, which includes out-of-the-box implementations for common CRUD (Create, Read, Update, Delete) operations

**Features**

1. Repository Abstraction: Provides a high-level abstraction over the data access layer, allowing developers to define repositories with minimal code.
2. Automatic Query Generation: Generates queries based on method names defined in repository interfaces.
3. Pagination and Sorting: Supports pagination and sorting out of the box.
4. Auditing: Supports auditing of entity changes (e.g., tracking created/modified dates and users).
5. Custom Query Methods: Allows custom JPQL (Java Persistence Query Language) and SQL queries.
6. Integration with Spring: Seamlessly integrates with the Spring Framework, including transaction management and dependency injection.

### Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project82/main/src/test/java/com/demo/project82/StudentTest.java" >}}

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project82/main/README.md" >}}

## References

[https://vladmihalcea.com/blog/](https://vladmihalcea.com/blog/)
[https://thorben-janssen.com/ultimate-guide-association-mappings-jpa-hibernate/](https://thorben-janssen.com/ultimate-guide-association-mappings-jpa-hibernate/)
