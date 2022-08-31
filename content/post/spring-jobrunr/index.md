---
title: 'Spring - JobRunr'
description: 'Spring - JobRunr'
summary: 'Spring boot integration with JobRunr'
date: '2022-06-02'
aliases: [/spring-jobrunr/]
author: 'Arjun Surendra'
categories: [JobRunr]
tags: [spring, spring-boot, jobrunr]
toc: true
---

Spring boot integration with JobRunr

Github: [https://github.com/gitorko/project59](https://github.com/gitorko/project59)

## JobRunr

Perform fire-and-forget, delayed, scheduled and recurring jobs inside Java applications using only Java 8 lambda's

1. It lets you schedule background jobs using lambda. 
2. The jobs can run on a distributed nodes, more node that join, the work gets distributed.
3. It serializes the lambda as JSON and stores it in db. 
4. It also contains an automatic retry feature with an exponential back-off policy for failed jobs. 
5. There is also a built-in dashboard that allows you to monitor all jobs.
6. It is self-maintaining, Successful jobs are automatically deleted after a configurable amount of time, so there is no need to perform manual storage cleanup.
7. The job details are stored in db.

## Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project59/main/src/main/java/com/demo/project59/HomeController.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project59/main/src/main/java/com/demo/project59/AppService.java" >}}

Open dashboard: [http://localhost:8000/dashboard/](http://localhost:8000/dashboard/)

![](img01.png)
![](img02.png)
![](img03.png)
![](img04.png)

## Setup

{{< markcode "https://raw.githubusercontent.com/gitorko/project59/main/README.md" >}}

## References

[https://www.jobrunr.io/en/](https://www.jobrunr.io/en/)
