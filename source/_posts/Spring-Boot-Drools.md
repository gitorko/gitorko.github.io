---
title: Spring Boot & Drools
date: 2019-03-29 00:00:00
tags: 
- drools
categories:
- [Drools]
---

A Spring boot application integration with Drools. Drools is a Business Rule Management System (BRMS). Business & Non-Technical users can write the rules in a format that is easy to understand and plug it into drools engine. These rules/facts are processed to produce results. Cost of changing the rules is low.

Github: [https://github.com/gitorko/project63](https://github.com/gitorko/project63)

<!-- more -->

<!-- toc -->

## Drools

{% ghcode https://github.com/gitorko/project63/blob/master/src/main/java/com/demo/project63/Application.java %}

{% ghcode https://github.com/gitorko/project63/blob/master/src/main/java/com/demo/project63/Product.java %}

{% ghcode https://github.com/gitorko/project63/blob/master/src/main/resources/product-discount.drl %}

Run the project

```bash
./gradlew bootRun
```
