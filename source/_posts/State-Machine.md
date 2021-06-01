---
title: State Machine
date: 2020-02-11 00:00:00
tags: 
- state machine
categories:
- [Design Pattern]
- [Spring]
---

State machine lets you move to different states based on the events, you can also have listeners registered that get notified on state change events and carry out certain actions.

Github: [https://github.com/gitorko/project77](https://github.com/gitorko/project77)

<!-- more -->

<!-- toc -->

## State machine

We will use the shopping cart state machine diagram as a reference to implement our code. If any invalid events are sent then an exception is thrown.

{% asset_img statemachine.png %}

## Code

Here we use state design pattern and observer pattern to design a state machine.

{% ghcode https://github.com/gitorko/project77/blob/master/src/main/java/com/demo/project77/simple/Application.java %}


We can also use the spring state machine libraries

{% ghcode https://github.com/gitorko/project77/blob/master/src/main/java/com/demo/project77/spring/Application.java %}

## References
[https://spring.io/projects/spring-statemachine](https://spring.io/projects/spring-statemachine)
