---
title: 'State Machine'
description: 'State Machine'
summary: 'Java based implementation of a state machine & spring library based implementation of state machine.'
date: '2020-02-12'
aliases: [/state-machine/]
author: 'Arjun Surendra'
categories: [Design-Pattern]
tags: [design-pattern, state-machine]
toc: true
---

Java based implementation of a state machine & spring library based implementation of state machine.
State machine lets you move to different states based on the events, you can also have listeners registered that get notified on state change events and carry out certain actions.

Github: [https://github.com/gitorko/project77](https://github.com/gitorko/project77)

## State Machine

We will use the shopping cart state machine diagram as a reference to implement our code. If any invalid events are sent then an exception is thrown.

![](statemachine.png)

## Code

Here we use state design pattern and observer pattern to design a state machine.

{{< ghcode "https://raw.githubusercontent.com/gitorko/project77/main/src/main/java/com/demo/project77/simple/Application.java" >}}

We can also use the spring state machine libraries

{{< ghcode "https://raw.githubusercontent.com/gitorko/project77/main/src/main/java/com/demo/project77/spring/Application.java" >}}

## References

[https://spring.io/projects/spring-statemachine](https://spring.io/projects/spring-statemachine)
