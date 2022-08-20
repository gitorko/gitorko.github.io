---
title: 'Spring Integration - Basics'
description: 'Spring Integration - Basics'
summary: 'Spring Integration provides a framework to support Enterprise Integration Patterns.'
date: '2022-07-30'
aliases: [/spring-integration-basics/]
author: 'Arjun Surendra'
categories: [Spring-Integration, Design-Pattern]
tags: [Enterprise-Integration-Patterns]
toc: true
---

Spring Integration provides a framework to support Enterprise Integration Patterns.

Github: [https://github.com/gitorko/project97](https://github.com/gitorko/project97)

## Spring Integration

1. Messaging support - All communication is treated as asynchronous messages sent between different channels. This provides loose coupling.
2. Support of external system - Adapters for ftp,file system, rabbitMQ and other external systems.

A higher level of abstraction over Spring’s support for remoting, messaging, and scheduling is provided so that developers dont have to write the code to interact with these systems but focus only on the business logic.
At any point if the source changes from a file system to an ftp server, the changes required will be very minimal.

Spring integration provides different ways to configure:

1. XML approach to wire 
2. Bean annotation approach
3. Java DSL approach (We will focus on DSL approach as its easier to read and maintain)

### Terminology

1. Message - Wrapper that can wrap a java object, contains payload & headers
2. Message Channel - A conduit for transmitting messages between producers & consumers
    a. Point-to-Point channel - one consumer should receive each message from a channel
    b. Publish/Subscribe channel - Will broadcast the message to any subscriber listening to that channel. 
3. Message Transform
4. Message Filter
5. Message Router - Determines what channel or channels (if any) should receive the message next
6. Message Bridge - Connects two message channels or channel adapters
7. Splitter
8. Aggregator
9. Handle - ServiceActivator that handle the message.
10. Adapter endpoints - Provide one-way integration.
11. Gateway endpoints - Provide two-way request/response integration.

### Types of Message Channel:

1. PollableChannel - Store messages, You need to ask for messages by polling i.e call receive method

    a. QueueChannel - Pollable, FIFO, Channel has multiple consumers, only one of them will receive, can be made blocking.
    b. PriorityChannel - Pollable, Messages to be ordered within the channel based on priority, Channel has multiple consumers, only one of them will receive, can be made blocking.
    c. RendezvousChannel - Pollable, Synchronous, Similar to QueueChannel but zero capacity to buffer, direct-handoff scenario, wherein a sender blocks until consumer invokes the channel’s receive() method

2. SubscribableChannel - Event driven, need to subscribe to get the messages. i.e call the subscribe method.

    a. Direct Channel - Subscribable, single subscriber, single thread behaviour blocking the sender thread until the message is subscribed.
    b. Publish Subscribe Channel - Subscribable, many subscribers, all will get the message.
    c. FixedSubscriberChannel - Subscribable, single subscriber, subscriber that cannot be unsubscribed

3. ExecutorChannel - Delegates to an instance of TaskExecutor to perform the dispatch
4. FluxChannel - Allows reactive consumption

## Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project97/main/src/main/java/com/demo/project97/integration/BasicIntegration.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project97/main/src/main/java/com/demo/project97/integration/FileIntegration.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project97/main/src/main/java/com/demo/project97/integration/JPAIntegration.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project97/main/src/main/java/com/demo/project97/integration/RabbitMQIntegration.java" >}}

## Setup

{{< markcode "https://raw.githubusercontent.com/gitorko/project97/main/README.md" >}}

## Testing

To test the function use the postman collection

[https://raw.githubusercontent.com/gitorko/project97/main/postman/Project97.postman_collection.json](https://raw.githubusercontent.com/gitorko/project97/main/postman/Project97.postman_collection.json)

## References

[https://spring.io/projects/spring-integration](https://spring.io/projects/spring-integration/)

[https://www.enterpriseintegrationpatterns.com/](https://www.enterpriseintegrationpatterns.com/)
