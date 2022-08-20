---
title: 'Spring Boot - Traefik (Rate Limit)'
description: 'Spring Boot - Traefik (Rate Limit)'
summary: 'Spring Boot application deployed on kubernetes, configured with Traefik ingress controller to rate limit.'
date: '2022-04-23'
aliases: [/spring-boot-traefik-rate-limit/]
author: 'Arjun Surendra'
categories: [Spring, Traefik, Kubernetes]
tags: [spring, traefik, rate-limit]
toc: true
---

Application deployed on kubernetes, configured with Traefik ingress controller to rate limit.

Github: [https://github.com/gitorko/project95](https://github.com/gitorko/project95)

## Traefik

Traefik is a reverse proxy and load balancer that makes deploying microservices easy.

We will deploy the spring rest application along with postgres db on kubernetes instance. Then we will configure Traefik as ingress controller and apply rate limit on it using Traefik Proxy Middleware.
We will use docker desktop kubernetes instance.

Rate limiting is a technique for controlling the rate of requests to your application. It can save you from  Denial-of-Service (DoS) or resource starvation problems. Without rate limits, a burst of traffic could bring down the whole service making it unavailable for everybody.

![](img01.png)

## Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project95/main/docker/deployment.yaml" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project95/main/docker/deployment-traefik.yaml" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project95/main/docker/deployment-traefik-ratelimit.yaml" >}}

## Setup

{{< markcode "https://raw.githubusercontent.com/gitorko/project95/main/README.md" >}}

## Testing

Deploy the image to kubernetes

![](img02.png)

![](img03.png)

The dashboard will show the HTTP Routers & the middleware rate limit config

![](img04.png)

You can also look at success rate

![](img05.png)

To test the rate limit functionality open the RateLimit.jmx file in JMeter and run the test

![](img06.png)

Create a user, the data is persisted in the postgres db.

```bash
curl --request POST 'http://localhost.com/rest/customer' \
--header 'Content-Type: application/json' \
--data-raw '{
    "firstName" : "John",
    "lastName" : "Doe",
    "city": "NY"
}'
```

Get the user

```bash
curl --request GET 'http://localhost.com/rest/customer'
```

## References

[https://traefik.io/](https://traefik.io/)
