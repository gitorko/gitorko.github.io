---
title: 'Clarity Server-Driven DataGrid'
description: 'Clarity Server-Driven DataGrid'
summary: 'Using Query DSL we will fetch page by page data and render it in clarity server-driven data grid'
date: '2021-10-30'
aliases: [/clarity-server-driven-datagrid/]
author: 'Arjun Surendra'
categories: [Clarity, QueryDSL]
tags: [server-driven, clarity, datagrid, querydsl]
toc: true
---

Clarity provides Server-Driven DataGrid. Using Query DSL we will fetch page by page data and render it in clarity server-driven data grid

Github: [https://github.com/gitorko/project86](https://github.com/gitorko/project86)

## Quick Overview

To deploy the application in a single command, clone the project, make sure no conflicting docker containers or ports are running and then run

```bash
git clone https://github.com/gitorko/project86
cd project86
docker-compose -f docker/docker-compose.yml up 
```

Open [http://localhost:8080/](http://localhost:8080/)

## Server-Driven DataGrid

When dealing with large amounts of data or heavy processing, a DataGrid often has to access the currently displayed data only, requesting only the necessary pieces of data from the server.

## Design

![](img01.png)

## Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project86/main/src/main/java/com/demo/project86/controller/HomeController.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project86/main/src/main/java/com/demo/project86/domain/Customer.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project86/main/src/main/java/com/demo/project86/domain/CustomerBinderCustomizer.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project86/main/src/main/java/com/demo/project86/repo/CustomerRepository.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project86/main/ui/src/app/components/home/home.component.html" >}}

The debounceTime added to debounce the events so that rest api doesn't get called for every keystroke.

{{< ghcode "https://raw.githubusercontent.com/gitorko/project86/main/ui/src/app/components/home/home.component.ts" >}}

## Setup

{{< markcode "https://raw.githubusercontent.com/gitorko/project86/main/README.md" >}}

## References

[https://clarity.design/](https://clarity.design/)

[https://clarity.design/angular-components/datagrid/#server-driven-datagrid](https://clarity.design/angular-components/datagrid/#server-driven-datagrid)
