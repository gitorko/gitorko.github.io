---
title: 'Distributed Locking - Apache Ignite'
description: 'Distributed Locking - Apache Ignite'
summary: 'Distributed Locking - Apache Ignite'
date: '2024-06-15'
aliases: [/distributed-locking-apache-ignite/]
author: 'Arjun Surendra'
categories: [Apache-Ignite, Spring]
tags: [ignite, distributed-lock, k8s, kubernetes]
toc: true
---

Spring boot application with distributed locking using apache ignite

Github: [https://github.com/gitorko/project04](https://github.com/gitorko/project04)

## Apache Ignite

Apache Ignite is a distributed database. It supports distributed locking mechanism.

![](logo.png)

### Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project04/main/src/main/java/com/demo/project04/config/IgniteConfig.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project04/main/src/main/java/com/demo/project04/service/LockService.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project04/main/docker/deployment.yaml" >}}

### Postman

![](img01.png)

Import the postman collection to postman

[Postman Collection](https://raw.githubusercontent.com/gitorko/project04/main/postman/Project04.postman_collection.json)

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project04/main/README.md" >}}

## References

[https://ignite.apache.org/](https://ignite.apache.org/)