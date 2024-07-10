---
title: 'Spring Reactor - Basics'
description: 'Spring Reactor - Basics'
summary: 'Reactive programming examples on how to use spring reactor.'
date: '2021-07-11'
aliases: [/spring-reactor-basics/]
author: 'Arjun Surendra'
categories: [Spring, Spring-Reactor]
tags: [spring, spring-reactor]
toc: true
---

Reactive programming examples on how to use spring reactor.

Github: [https://github.com/gitorko/project83](https://github.com/gitorko/project83)

## Spring Reactor

Spring Reactor is a library for building non-blocking, reactive applications in Java.
Reactor is used in Spring WebFlux, which is the reactive web framework included in Spring 5.

**Features**

1. Reactive Streams: Reactor is based on the Reactive Streams specification, which defines a standard for asynchronous stream processing with non-blocking backpressure.
2. Mono and Flux: Mono represents a single value or an empty result (similar to Optional). Flux represents a stream of 0 to N elements.
3. Functional API: Reactor provides a rich set of operators that allow you to manipulate, transform, and compose reactive streams in a functional style.
4. Non-blocking: Reactor is designed to work in a non-blocking manner, making it suitable for applications that need to handle a large number of concurrent I/O operations.
5. Backpressure: Reactor supports backpressure, a mechanism to ensure that a producer does not overwhelm a consumer with too much data.

### Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project83/main/src/test/java/com/demo/project83/ReactorTest.java" >}}

## References

[https://projectreactor.io/](https://projectreactor.io/)
