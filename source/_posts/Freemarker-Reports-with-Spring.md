---
title: Freemarker Reports with Spring
date: 2020-08-28 00:00:00
tags:
- freemarker
categories:
- [Reports]
---

Using freemarker templates we can generate basic html reports that can be downloaded. 

Github: [https://github.com/gitorko/project69](https://github.com/gitorko/project69)

<!-- more -->

<!-- toc -->

## Freemarker template reports 

We will generate a single html file report using freemarker template and provide a rest url to download the report.

## Code

{% ghcode https://github.com/gitorko/project69/blob/master/src/main/java/org/gitokro/project69/Application.java 25 75 %}

&nbsp;

{% ghcode https://github.com/gitorko/project69/blob/master/src/main/resources/templates/my-report.ftl %}

Run the project and access the url in a browser to download the report.

```bash
./gradlew bootRun
```

[http://localhost:8080/report](http://localhost:8080/report)

{% asset_img image01.png %}

## References

[https://freemarker.apache.org/](https://freemarker.apache.org/)
