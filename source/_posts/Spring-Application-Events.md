---
title: Spring Application Events
date: 2020-08-06 00:00:00
tags:
- events
categories:
- [Spring]
---

Spring application events provides functionality for async tasks to communicate with each other.

Github: [https://github.com/gitorko/project73](https://github.com/gitorko/project73)

<!-- more -->

<!-- toc -->

## Spring Application Events

Lets say you have 2 async functions that needs co-ordinate. When async1 is complete you need async2 to pick up the processed data and process it. You could use an external queue like rabbitmq, or you could write a producer consumer. Spring events lets you handle such a task easily. Do note that this will be in-memory so if the server restarts all events will be lost.

## Code

{% ghcode https://github.com/gitorko/project73/blob/master/src/main/java/com/demo/project73/Application.java %}

Run the project

```bash
./gradlew bootRun
```

You will now see that after doSomeAsyncTask1 is run it publishes a event and doSomeAsyncTask2 picks up the event and both run asynchronously.
```
Triggered when application ready!
Running task!
Trigger event: 0
Received event MyEvent(name=EventId: 0)
Trigger event: 1
Received event MyEvent(name=EventId: 1)
Trigger event: 2
Received event MyEvent(name=EventId: 2)
Trigger event: 3
Received event MyEvent(name=EventId: 3)
Trigger event: 4
Received event MyEvent(name=EventId: 4)
Processed event MyEvent(name=EventId: 0)
Processed event MyEvent(name=EventId: 1)
Processed event MyEvent(name=EventId: 2)
Processed event MyEvent(name=EventId: 3)
Processed event MyEvent(name=EventId: 4)
```

## References
Spring Application Events : [https://spring.io/blog/2015/02/11/better-application-events-in-spring-framework-4-2](https://spring.io/blog/2015/02/11/better-application-events-in-spring-framework-4-2)
