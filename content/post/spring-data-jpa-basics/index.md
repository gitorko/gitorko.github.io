---
title: 'Spring Data JPA - Basics'
description: 'Spring Data JPA - Basics'
summary: 'Spring Data JPA - Basics'
date: '2024-06-12'
aliases: [/spring-data-jpa-basics/]
author: 'Arjun Surendra'
categories: [Spring, JPA, Hibernate]
tags: [jdbc, onetomany, manytoone, onetoone, jointable, locking, transactional]
toc: true
draft: false
---

Introduction to Spring JPA with examples. Create domain classes that map to database & write JPA queries to fetch the data.

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

| Annotation    | Description                                                                                                                                                                                            |
|:--------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `@ManyToOne`  | Most natural way to map a foreign key relation. Default to FETCH.EAGER                                                                                                                                 |
| `@OneToMany`  | Parent entity to map collection of child entities. If bi-directional then child entity has @ManyToOne. If child entities can grow then will affect performance. Use only when child entities are few   |
| `@OneToOne`   | Creates a foreign key in parent table that refers to the primary key of child table.                                                                                                                   |
| `@MapsId`     | Single key acts as primary key & foreign key, with single key you can now fetch data from both table with same key.                                                                                    |
| `@ManyToMany` | Two parents on one child, avoid doing CascadeType.ALL, dont do orphan removal.                                                                                                                         |
| `mappedBy`    | Present in parent, Tells hibernate that the child side is in charge of handling bi-directional association. mappedBy & @JoinColumn cant be present in the same class.                                  |

For bi-directional associations where the child is in charge of handling association, you must still write setter methods in parent to sync both sides. Otherwise, you risk very subtle state propagation issues. 

Spring JPA determines of an object is new based on `@Version` annotation, you can also accomplish the same by implementing `Persistable` interface.

Spring JPA uses dirty checking mechanism to determine if something has changed and then auto saves the data to database. 
Dirty checking default all columns as updated, if you want avoid it use the annotation `@DynamicUpdate` on the class.

## Locking & Transaction Isolation

{{< embed "content/post/optimistic-pessimistic-locking/common.md" >}}

### Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project82/main/src/test/java/com/demo/project82/StudentTest.java" >}}

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project82/main/README.md" >}}

## References

[https://vladmihalcea.com/blog/](https://vladmihalcea.com/blog/)

[https://thorben-janssen.com/ultimate-guide-association-mappings-jpa-hibernate/](https://thorben-janssen.com/ultimate-guide-association-mappings-jpa-hibernate/)

[https://docs.spring.io/spring-data/jpa/reference/repositories/projections.html](https://docs.spring.io/spring-data/jpa/reference/repositories/projections.html)