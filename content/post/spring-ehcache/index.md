---
title: 'Spring - EhCache'
description: 'Spring - EhCache'
summary: 'Spring Boot integration with EhCache 3'
date: '2022-08-04'
aliases: [/spring-ehcache/]
author: 'Arjun Surendra'
categories: [Caching]
tags: [spring, spring-boot, ehcache]
toc: true
---

Spring Boot integration with EhCache 3

Github: [https://github.com/gitorko/project98](https://github.com/gitorko/project98)

## EhCache

EhCache is an open-source cache library. It supports cache in memory and disk, It supports eviction policies such as LRU, LFU, FIFO. Ehcache uses Last Recently Used (LRU) eviction strategy for memory & Last Frequently Used (LFU) as the eviction strategy for disk store.

Ehcache Caching Tiers - Caching layer can consist of more than one memory area. When using more than one memory area, the areas are arranged as hierarchical tiers. 
The lowest tier is called the Authority Tier and the other tiers are called the Near Cache. Most frequently used data is stored in the fastest caching tier (top layer)

### Types of store

![](cache-store.png)

1. On-Heap Store - stores cache entries in Java heap memory
2. Off-Heap Store -  primary memory (RAM) to store cache entries, cache entries will be moved to the on-heap memory automatically before they can be used.
3. Disk Store - uses a hard disk to store cache entries. SSD type disk would perform better.
4. Clustered Store - stores cache entries on the remote server

### Types of caching

![](cache-strategy.png)

1. Read-Cache-aside - Application queries the cache. If the data is found, it returns the data directly. If not it fetches the data from the SoR, stores it into the cache, and then returns.
2. Read-Through - Application queries the cache, cache service queries the SoR if not present and updates the cache and returns.
3. Write-Around - Application writes to db and to the cache.
4. Write-Behind / Write-Back - Application writes to cache. Cache is pushed to SoR after some delay periodically.
5. Write-through - Application writes to cache, cache service immediately writes to SoR.

## Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project98/main/src/main/java/com/demo/project98/service/CountryCache.java" >}}
{{< ghcode "https://raw.githubusercontent.com/gitorko/project98/main/src/main/java/com/demo/project98/service/CountryCacheListener.java" >}}
{{< ghcode "https://raw.githubusercontent.com/gitorko/project98/main/src/main/java/com/demo/project98/Main.java" >}}
{{< ghcode "https://raw.githubusercontent.com/gitorko/project98/main/src/main/resources/ehcache.xml" >}}

Notice the SQL is printed each time a db call happens, if the data is cached no DB call is made.

## Setup

{{< markcode "https://raw.githubusercontent.com/gitorko/project98/main/README.md" >}}

## Postman

Import the postman collection to postman

[Postman Collection](https://raw.githubusercontent.com/gitorko/project98/main/postman/Project98.postman_collection.json)

## References

[https://www.ehcache.org/documentation/3.0](https://www.ehcache.org/documentation/3.0)

[https://docs.spring.io/spring-boot/docs/2.7.2/reference/htmlsingle/#io.caching](https://docs.spring.io/spring-boot/docs/2.7.2/reference/htmlsingle/#io.caching)
