---
title: 'Spring Events - Modulith'
description: 'Spring Events'
summary: 'Spring events provides event handling mechanism in spring'
date: '2020-08-07'
aliases: [/spring-events/]
author: 'Arjun Surendra'
categories: [Spring, Spring-Modulith, Spring-Events]
tags: [spring, spring-modulith]
toc: true
---

Spring events provides event handling mechanism in spring.

Github: [https://github.com/gitorko/project73](https://github.com/gitorko/project73)

## Spring Events

Spring events ensures loose coupling in an application, it allows inter-module interaction.
Instead of injecting different `@Service` beans and invoking them in the directly you now publish an event and all other places that need to process it will implement a listener.

Service can be developed without all the implementations. 
Eg: Audit logging service is being developed and not ready, hence instead of being blocked on developing the core customer service class, just publish an event and when the service is ready add a listener to process that event. 

Spring events are in-memory so if the server restarts all events published will be lost. 
With Spring Modulith library you can now persist such event and process them after a restart.
A module can access the content of any other module but can't access sub-packages of other modules.

By default `@EventListener` run on the same thread as the caller, to run it asynchronously use `@Async`

`@ApplicationModuleListener` by default comes with `@Transactional`, `@Async` & `@TransactionalEventListener` annotation enabled.

To process the events on restart enable this flag.

```yaml
spring:
  modulith:
    republish-outstanding-events-on-restart: true
```

### Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project73/main/src/main/java/com/demo/project73/listener/ApplicationEventListener.java" >}}
{{< ghcode "https://raw.githubusercontent.com/gitorko/project73/main/src/main/java/com/demo/project73/listener/AuditEventListener.java" >}}
{{< ghcode "https://raw.githubusercontent.com/gitorko/project73/main/src/main/java/com/demo/project73/listener/CustomEventListener.java" >}}
{{< ghcode "https://raw.githubusercontent.com/gitorko/project73/main/src/main/java/com/demo/project73/listener/ObjectEventListener.java" >}}

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project73/main/README.md" >}}

## References

[https://spring.io/blog/2015/02/11/better-application-events-in-spring-framework-4-2](https://spring.io/blog/2015/02/11/better-application-events-in-spring-framework-4-2)
[https://spring.io/projects/spring-modulith](https://spring.io/projects/spring-modulith)