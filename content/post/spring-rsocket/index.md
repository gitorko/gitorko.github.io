---
title: 'Spring - Rsocket'
description: 'Spring - Rsocket'
summary: 'Spring - Rsocket'
date: '2024-06-16'
aliases: [/spring-rsocket/, /spring-boot-rsocket/]
author: 'Arjun Surendra'
categories: [Rsocket, Spring]
tags: [rsocket, spring]
toc: true
---

Spring boot client server application with rsocket

Github: [https://github.com/gitorko/project02](https://github.com/gitorko/project02)

## Rsocket

RSocket is a binary & message passing protocol for multiplexed, duplex communication over TCP, WebSocket, and other byte stream transports.

**Interaction Models**

| Type             | Description                                            |
|:-----------------|:-------------------------------------------------------|
| Request-Response | send one message and receive one back                  |
| Request-Stream   | send one message and receive a stream of messages back |
| Channel          | send streams of messages in both directions            |
| Fire-and-Forget  | send a one-way message                                 |

**Key features of RSocket protocol**

1. Reactive Streams - back pressure allows a requester to slow down a responder at the source, hence reducing reliance on network layer congestion control, and the need for buffering at the network level or at any level.
2. Request throttling - "Leasing" after the LEASE frame that can be sent from each end to limit the total number of requests allowed by other end for a given time. Leases are renewed periodically.
3. Session resumption - loss of connectivity and requires some state to be maintained.
4. Fragmentation - re-assembly of large messages.
5. Keepalive - heartbeats.

**Differences**

| RSocket                                   | GRPC                              | Rest              |
|:------------------------------------------|:----------------------------------|:------------------|
| Binary Protocol (TCP, a File, WebSockets) | Works on HTTP2 (Protocol Buffers) | Works on HTTP/1.1 |
| Works on 5/6 layer of OSI model           | Works on 7 layer of OSI model     |                   |
| Support Back pressure handling            |                                   |                   |


### Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project02/main/rserver/src/main/java/com/demo/project02/rserver/controller/GreetingController.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project02/main/rclient/src/main/java/com/demo/project02/rclient/RclientApp.java" >}}

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project02/main/README.md" >}}

## References

[https://docs.spring.io/spring-framework/reference/rsocket.html](https://docs.spring.io/spring-framework/reference/rsocket.html)

[https://medium.com/netifi/differences-between-grpc-and-rsocket-e736c954e60](https://medium.com/netifi/differences-between-grpc-and-rsocket-e736c954e60)