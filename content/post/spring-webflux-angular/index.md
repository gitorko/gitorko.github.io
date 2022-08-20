---
title: 'Spring Webflux & Angular'
description: 'Spring Webflux & Angular'
summary: 'Spring Reactive web application with angular clarity and & reactive mongo db.'
date: '2021-11-06'
aliases: [/spring-webflux-angular/]
author: 'Arjun Surendra'
categories: [Spring, Spring-Webflux]
tags: [webflux, clarity, angular, mongodb]
toc: true
---

Spring Reactive web application with angular clarity and & reactive mongo db. 
Creates uber jar to deploy.

Github: [https://github.com/gitorko/project60](https://github.com/gitorko/project60)

## Quick Overview

To deploy the application in a single command, clone the project, make sure no conflicting docker containers or ports are running and then run

```bash
git clone https://github.com/gitorko/project60
cd project60
docker-compose -f docker/docker-compose.yml up 
```

Open [http://localhost:8080/](http://localhost:8080/)

## Features

Clarity is an open source library that provides various Angular components.

![](img01.png)

## Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project60/main/src/main/java/com/demo/project60/Main.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project60/main/src/main/java/com/demo/project60/repository/CustomerRepository.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project60/main/src/main/resources/application.yaml" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project60/main/ui/src/app/services/rest.service.ts" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project60/main/ui/src/app/home/home.component.html" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project60/main/ui/src/app/home/home.component.ts" >}}

## Setup

{{< markcode "https://raw.githubusercontent.com/gitorko/project60/main/README.md" >}}

## References

[Angular](https://angular.io/tutorial/)
[Clartiy](https://clarity.design/)
[Spring Boot](https://spring.io/projects/spring-boot)
[Spring Webflux](https://docs.spring.io/spring-framework/docs/current/reference/html/web-reactive.html)
