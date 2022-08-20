---
title: 'Spring Boot - Drools'
description: 'Spring Boot - Drools'
summary: 'Spring boot integration with Drools.'
date: '2019-03-30'
aliases: [/spring-boot-drools/]
author: 'Arjun Surendra'
categories: [Drools]
tags: [spring, drools]
toc: true
---

Spring boot integration with Drools.
Drools is a Business Rule Management System (BRMS). Business & Non-Technical users can write the rules in a format that is easy to understand and plug it into drools engine. These rules/facts are processed to produce results. Cost of changing the rules is low.

Github: [https://github.com/gitorko/project63](https://github.com/gitorko/project63)

## Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project63/main/src/main/java/com/demo/project63/Main.java" >}}
{{< ghcode "https://raw.githubusercontent.com/gitorko/project63/main/src/main/resources/product-discount.drl" >}}

Run the project

```bash
./gradlew bootRun
```

## References

[https://www.drools.org/](https://www.drools.org/)
