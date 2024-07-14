---
title: 'Spring - EhCache'
description: 'Spring - EhCache'
summary: 'Spring Boot integration with EhCache 3'
date: '2024-06-23'
aliases: [/spring-ehcache/]
author: 'Arjun Surendra'
categories: [Caching]
tags: [spring, spring-boot, ehcache]
toc: true
---

Spring Boot 3 with EhCache 3

Github: [https://github.com/gitorko/project98](https://github.com/gitorko/project98)

## EhCache

EhCache is an open-source cache library. Ehcache version 3 provides an implementation of a JSR-107 cache manager. 
It supports cache in memory and disk, It supports eviction policies such as LRU, LFU, FIFO. Ehcache uses Last Recently Used (LRU) eviction strategy for memory & Last Frequently Used (LFU) as the eviction strategy for disk store.

## Caching

{{< embed "content/post/spring-ehcache/common.md" >}}

## Spring Caching

**@Cacheable vs @CachePut**

`@Cacheable` will skip running the method, whereas `@CachePut` will actually run the method and then put its results in the cache.

You can also use `CacheEventListener` to track events like CREATED, UPDATED, EXPIRED, REMOVED.

Ehcache uses Last Recently Used (LRU) as the default eviction strategy for the memory stores when the cache is full.
If a disk store is used and this is full it uses Last Frequently Used (LFU) as the eviction strategy.

You can enable spring actuator and look at the cache metrics

The `@CacheConfig` annotation allows us to define certain cache configurations at the class level. This is useful if certain cache settings are common for all methods.

```bash
@CacheConfig(cacheNames = "customerCache")
```

### Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project98/main/src/main/java/com/demo/project98/config/CacheConfig.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project98/main/src/main/java/com/demo/project98/listener/CountryCacheListener.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project98/main/src/main/java/com/demo/project98/service/CountryService.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project98/main/src/main/java/com/demo/project98/service/CustomerService.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project98/main/src/main/java/com/demo/project98/service/NumberService.java" >}}

Notice the SQL is printed each time a db call happens, if the data is cached no DB call is made.

### Postman

Import the postman collection to postman

[Postman Collection](https://raw.githubusercontent.com/gitorko/project98/main/postman/Project98.postman_collection.json)

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project98/main/README.md" >}}

## References

[https://www.ehcache.org/documentation/3.0](https://www.ehcache.org/documentation/3.0)

[https://docs.spring.io/spring-boot/docs/2.7.2/reference/htmlsingle/#io.caching](https://docs.spring.io/spring-boot/docs/2.7.2/reference/htmlsingle/#io.caching)
