---
title: 'Spring - QueryDSL'
description: 'Spring - QueryDSL'
summary: 'Spring Boot QueryDSL lets you query the database using domain specific language similar to SQL.'
date: '2020-08-08'
aliases: [/spring-boot-querydsl/,/spring-querydsl/]
author: 'Arjun Surendra'
categories: [Spring]
tags: [spring, spring-boot, querydsl]
toc: true
---

Spring Boot QueryDSL lets you query the database using domain specific language similar to SQL.

Github: [https://github.com/gitorko/project75](https://github.com/gitorko/project75)

## Spring QueryDSL

Let's say you used Spring Data to query the db by using spring naming convention. 
If your table has 100's of column and you have to query by any column you can't write 100 access functions. This is where query dsl comes into play.

## Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project75/main/src/main/java/com/demo/project75/Main.java" >}}

It uses in memory h2 db to persist.

## Setup

{{< markcode "https://raw.githubusercontent.com/gitorko/project75/main/README.md" >}}

## Testing

You can now search based on all the columns of the db and get the response.

[http://localhost:8080/users?age=30](http://localhost:8080/users?age=30)

[http://localhost:8080/users?firstName=firstname_0](http://localhost:8080/users?firstName=firstname_0)

[http://localhost:8080/users?firstName=firstname_0&age=30](http://localhost:8080/users?firstName=firstname_0&age=30)

## References

Spring Query DSL : [http://www.querydsl.com/static/querydsl/latest/reference/html/ch02.html](http://www.querydsl.com/static/querydsl/latest/reference/html/ch02.html)

