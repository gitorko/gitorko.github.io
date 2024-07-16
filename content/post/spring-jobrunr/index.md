---
title: 'Spring - JobRunr'
description: 'Spring - JobRunr'
summary: 'Spring boot integration with JobRunr'
date: '2024-05-02'
aliases: [/spring-jobrunr/]
author: 'Arjun Surendra'
categories: [JobRunr]
tags: [spring, spring-boot, jobrunr, retry, work-distribution, postgres]
toc: true
---

Spring Boot 3 integration with JobRunr

Github: [https://github.com/gitorko/project59](https://github.com/gitorko/project59)

## JobRunr

JobRunr is a distributed job scheduler. If a service runs on many nodes the JobRunr ensure that a scheduled job is run only on a single instance. 
If you run a spring `@Scheduled` annotation then all instances will start the same job, you can use shedlock library to prevent this but this requires extra code.

**Types of Job**

1. Fire-Forget
2. Delayed
3. Recurring Job

**Advantages**

1. It lets you schedule background jobs using lambda.
2. The jobs can run on a distributed nodes, more node that join, the work gets distributed.
3. It serializes the lambda as JSON and stores it in db. 
4. It also contains an automatic retry feature with an exponential back-off policy for failed jobs. 
5. There is also a built-in dashboard that allows you to monitor all jobs.
6. It is self-maintaining, Successful jobs are automatically deleted after a configurable amount of time, so there is no need to perform manual storage cleanup.
7. The job details are stored in db.

### Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project59/main/src/main/java/com/demo/project59/controller/HomeController.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project59/main/src/main/java/com/demo/project59/service/AppService.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project59/main/src/main/resources/application.yaml" >}}

Open dashboard: [http://localhost:8000/dashboard/](http://localhost:8000/dashboard/)

![](cron.png)

![](img01.png)
![](img02.png)
![](img03.png)
![](img04.png)
![](img05.png)
![](img06.png)
![](img07.png)
![](img08.png)

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project59/main/README.md" >}}

## References

[https://www.jobrunr.io/en/](https://www.jobrunr.io/en/)
[https://www.jobrunr.io/en/documentation/configuration/spring/](https://www.jobrunr.io/en/documentation/configuration/spring/)
