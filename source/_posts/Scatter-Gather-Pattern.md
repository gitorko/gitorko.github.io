---
title: Scatter Gather Pattern
date: 2020-08-11 00:00:00
tags:
- scatter gather
categories:
- [Design Pattern]
---

Scatter Gather enterprise integration pattern is used for scenarios such as “best quote”, where we need to request information from several suppliers and decide which one provides us with the best price for the requested item.

Github: [https://github.com/gitorko/project62](https://github.com/gitorko/project62)

<!-- more -->

<!-- toc -->

## Scatter Gather Pattern

So we have a book product and we need to fetch the price from various sources and at max we can wait for 3 seconds. You could use a Thread.sleep or Threads join() method but then if the tasks complete before 3 seconds the tasks will still wait for 3 seconds before returning.

## Code

We can use a CountDownLatch to wait for the prices to be fetched. It will wait only for 3 seconds and return the prices fetched.

{% ghcode https://github.com/gitorko/project62/blob/master/src/main/java/com/demo/project62/scattergather/latch/ScatterGatherLatch.java %}

We can also use the invokeAll method

{% ghcode https://github.com/gitorko/project62/blob/master/src/main/java/com/demo/project62/scattergather/invoke/ScatterGatherInvoke.java %}

We can also use the CompletableFuture.

{% ghcode https://github.com/gitorko/project62/blob/master/src/main/java/com/demo/project62/scattergather/completable/ScatterGatherCompletable.java %}

Result

```
{amazon=2.35, flipkart=2.1}
```
