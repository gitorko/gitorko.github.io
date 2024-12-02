---
title: 'Spring Virtual Threads'
description: 'Spring Virtual Threads'
summary: 'Spring Boot with Java Virtual Threads'
date: '2024-06-01'
aliases: [/spring-virtual-threads/]
author: 'Arjun Surendra'
categories: [VirtualThreads, Spring, JDK21]
tags: [virtual-threads, spring, jdk21, auditing, liquibase, jacoco, spotbugs, checkstyle]
toc: true
featured: true
draft: false
---

## Virtual Threads

Virtual threads (Project Loom) is part of Java 21. Any blocking operation doesn't cause the thread to block. Thread Pool are replaced with a virtual thread executor.

Github: [https://github.com/gitorko/project58](https://github.com/gitorko/project58)

To enable virtual threads in spring boot application

```bash
spring.threads.virtual.enabled=true
```

### Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project58/main/src/main/java/com/demo/project58/controller/CustomerController.java" >}}
{{< ghcode "https://raw.githubusercontent.com/gitorko/project58/main/src/main/resources/application.yaml" >}}

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project58/main/README.md" >}}

## References

[https://spring.io/blog/2022/10/11/embracing-virtual-threads](https://spring.io/blog/2022/10/11/embracing-virtual-threads)