---
title: 'Scatter Gather Pattern'
description: 'Scatter Gather Pattern'
summary: 'Scatter Gather enterprise integration pattern implementation'
date: '2020-08-12'
aliases: [/scatter-gather-pattern/]
author: 'Arjun Surendra'
categories: [Design-Pattern]
tags: [design-pattern]
toc: true
---

Scatter Gather enterprise integration pattern is used for scenarios such as "best quote", where we need to request information from several suppliers and decide which one provides us with the best price for the requested item.

Github: [https://github.com/gitorko/project62](https://github.com/gitorko/project62)

## Scatter Gather Pattern

So we have a book product and we need to fetch the price from various sources and at max we can wait for 3 seconds. You could use a Thread.sleep or Threads join() method but then if the tasks complete before 3 seconds the tasks will still wait for 3 seconds before returning.

## Code

We can use a CountDownLatch to wait for the prices to be fetched. It will wait only for 3 seconds and return the prices fetched.

{{< ghcode "https://raw.githubusercontent.com/gitorko/project62/main/src/main/java/com/demo/project62/scattergather/latch/ScatterGatherLatch.java" >}}

We can also use the invokeAll method

{{< ghcode "https://raw.githubusercontent.com/gitorko/project62/main/src/main/java/com/demo/project62/scattergather/invoke/ScatterGatherInvoke.java" >}}

We can also use the CompletableFuture.

{{< ghcode "https://raw.githubusercontent.com/gitorko/project62/main/src/main/java/com/demo/project62/scattergather/completable/ScatterGatherCompletable.java" >}}

Result

```bash
{amazon=2.35, flipkart=2.1}
```
