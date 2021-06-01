---
title: Spring Boot Querydsl
date: 2020-08-07 00:00:00
tags:
- spring query dsl
categories:
- [Query DSL]
---

Spring Boot Querydsl lets you query the database using domain specific language similar to SQL.

Github: [https://github.com/gitorko/project75](https://github.com/gitorko/project75)

<!-- more -->

<!-- toc -->

## Spring Querydsl

Lets say you used Spring Data to query the db by using spring naming convention. However if you table has 100 columns and you have to query by any column you cant write 100 functions. This is where query dsl comes into play.

## Code

{% ghcode https://github.com/gitorko/project75/blob/master/src/main/java/com/demo/project75/Application.java %}

Run the project

```bash
./gradlew bootRun
```

You can now search based on all the columns of the db and get the response. 

[http://localhost:8080/users?age=30](http://localhost:8080/users?age=30)
[http://localhost:8080/users?age=firstname_0](http://localhost:8080/users?age=firstname_0)

## References

Spring Query DSL : [http://www.querydsl.com/static/querydsl/latest/reference/html/ch02.html](http://www.querydsl.com/static/querydsl/latest/reference/html/ch02.html)
