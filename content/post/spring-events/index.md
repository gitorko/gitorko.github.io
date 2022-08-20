---
title: 'Spring Events'
description: 'Spring Events'
summary: 'Spring events provides event handling mechanism in spring'
date: '2020-08-07'
aliases: [/spring-events/]
author: 'Arjun Surendra'
categories: [Spring]
tags: [spring]
toc: true
---

Spring events provides event handling mechanism in spring.

Github: [https://github.com/gitorko/project73](https://github.com/gitorko/project73)

## Spring Events

Spring events lets you create and consume events within an application thus providing loose coupling. 
Let's say you have 2 async functions that needs co-ordinate. When async1 is complete you need async2 to pick up the processed data and process it. You could use an external queue like rabbitmq, or you could write a producer consumer. Spring events lets you handle such a task easily. 
Do note that this will be in-memory so if the server restarts all events will be lost.

## Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project73/main/src/main/java/com/demo/project73/CustomEventListener.java" >}}
{{< ghcode "https://raw.githubusercontent.com/gitorko/project73/main/src/main/java/com/demo/project73/CustomEvent.java" >}}
{{< ghcode "https://raw.githubusercontent.com/gitorko/project73/main/src/main/java/com/demo/project73/CustomAsync.java" >}}

Run the project

```bash
./gradlew bootRun
```

The doSomeAsyncTask1 posts 2 events on the async thread. Both these events are then processed on different async threads.

## References

[https://spring.io/blog/2015/02/11/better-application-events-in-spring-framework-4-2](https://spring.io/blog/2015/02/11/better-application-events-in-spring-framework-4-2)
