---
title: 'RabbitMQ Streams'
description: 'RabbitMQ Streams'
summary: 'RabbitMQ Stream implementation'
date: '2022-07-10'
aliases: [/rabbitmq-stream/]
author: 'Arjun Surendra'
categories: [RabbitMQ]
tags: [streams]
toc: true
---

RabbitMQ Stream implementation.

Streams implement append-only log, messages are persistent and replicated.  

1. Large fan-outs - Deliver the same message to multiple subscribers
2. Replay / Time-travelling - Read messages from any point.
3. Throughput Performance - Log based messaging deliver performance compared to traditional queues.
4. Large logs - Streams are designed to store larger amounts of data in an efficient manner with minimal in-memory overhead.

Github: [https://github.com/gitorko/project74](https://github.com/gitorko/project74)

## RabbitMQ Stream

{{< ghcode "https://raw.githubusercontent.com/gitorko/project74/main/src/main/java/com/demo/project74/AsyncService.java" >}}

## Setup

{{< markcode "https://raw.githubusercontent.com/gitorko/project74/main/README.md" >}}

## References

[https://www.rabbitmq.com/streams.html](https://www.rabbitmq.com/streams.html)
