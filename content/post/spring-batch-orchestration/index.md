---
title: 'Spring Batch - Multi Stage Job Orchestration'
description: 'Spring Batch - Multi Stage Job Orchestration'
summary: 'Spring Batch - Multi Stage Job Orchestration'
date: '2024-06-18'
aliases: [/spring-batch-orchestration/]
author: 'Arjun Surendra'
categories: [SpringBatch]
tags: [spring, spring-boot, retry, orchestration, workflow, postgres]
toc: true
---

Spring boot implementation of job workflow using spring batch

Github: [https://github.com/gitorko/project67](https://github.com/gitorko/project67)

## Spring Batch

Spring batch is typically used to process data (read-process-write) in the background. 
Here we will use it as a workflow orchestration engine to execute jobs that take long time to complete for non-batch oriented flow.

As an example we take a travel booking flow, where a customer books flight, hotel, cab in a single click but the actual bookings are done in the background flow.
We can use an event based model but the challenges with event model is that its difficult to restart a specific job, It's difficult to view the event queues and see which jobs are stuck. 
We might even have to use priority queue to move some event ahead of the queue if the queue has a huge backlog.

So we will use spring batch which provides Job & Step flow to orchestrate our booking flow.

![](travel-booking-flow.png)

Features:

1. Retry will be attempted in case of failure with transactional rollback.
2. Jobs can be restarted
3. You can write if-else flows in the logic

After you run the code you see the jobs complete.

![](img01.png)

### Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project67/main/src/main/java/com/demo/project67/task/BookCabTask.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project67/main/src/main/java/com/demo/project67/task/BookFlightTask.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project67/main/src/main/java/com/demo/project67/task/FlightNotificationTask.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project67/main/src/main/java/com/demo/project67/workflow/NotificationWorkflow.java" >}}

### Postman

Import the postman collection to postman

[Postman Collection](https://raw.githubusercontent.com/gitorko/project67/main/postman/Project67.postman_collection.json)

![](img02.png)

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project67/main/README.md" >}}

## References

[https://spring.io/projects/spring-batch](https://spring.io/projects/spring-batch)
