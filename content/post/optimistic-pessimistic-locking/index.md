---
title: 'Spring JPA - Optimistic vs Pessimistic Locking'
description: 'Spring JPA -  Optimistic vs Pessimistic Locking'
summary: 'Spring JPA - Optimistic vs Pessimistic Locking'
date: '2024-06-12'
aliases: [/optimistic-pessimistic-locking/]
author: 'Arjun Surendra'
categories: [Locking]
tags: [optimistic-locking, pessimistic-locking, JPA]
toc: true
---

When an app is deployed on more than one server how to you ensure that 2 threads dont modify the same record in db? If the operation was performed on a single JVM you could look at locking but since there are many jvm the locking has to be done at database level.

Github: [https://github.com/gitorko/project82](https://github.com/gitorko/project82)

## Locking & Transaction Isolation

{{< embed "content/post/optimistic-pessimistic-locking/common.md" >}}

### Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project82/main/src/main/java/com/demo/project82/Main.java" >}}

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project82/main/README.md" >}}

## References

[https://spring.io/projects/spring-data-jpa](https://spring.io/projects/spring-data-jpa)
