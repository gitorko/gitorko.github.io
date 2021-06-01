---
title: Spring Boot Web - Thymeleaf
date: 2021-05-22 00:00:00
tags:
- spring mvc
- thymeleaf
- chart.js
- spring jpa
- hsql db
- bootstrap 5
- spring security
categories:
- [Web]
- [Spring]
---

A Spring boot web application with thymeleaf template & basic spring security support, uses bootstrap for CSS and chart.js for rendering charts. Creates uber jar to deploy.

Github: [https://github.com/gitorko/project79](https://github.com/gitorko/project79)

<!-- more -->

<!-- toc -->

## Spring Web

{% asset_img spring.png %}

A Spring Web MVC application that renders thymeleaf templates as HTML. Supports basic integration with spring security and provides login logout support.
Uses Spring Data to persist data into the HSQL db. A file based HSQL server db is used so that data persists across restarts. This can easily be changed to in-memory HSQL db.
Spring dev tools allow seamless reload on any changes for html and java files so you can view the changes in the browser as soon as you edit them.

Features:

1. Supports basic login via spring security
2. Bootstrap 5
3. Login screen
4. CRUD UI for adding and removing customer
5. HSQL db
6. Spring JPA
7. Thymeleaf template
8. Chart.js charts for bar,pie,stack charts with data from rest api

On Intellij to allow spring dev tools to reload on change you need to enable 'Update classes and resources' as shown below
{% asset_img image01.png %}

Spring MVC controller renders the HTML.

{% ghcode https://github.com/gitorko/project79/blob/master/src/main/java/com/demo/project79/controller/HomeController.java 29 35 %}

Spring security is configured for BASIC authentication

{% ghcode https://github.com/gitorko/project79/blob/master/src/main/java/com/demo/project79/config/WebSecurityConfig.java 19 34 %}

## chart.js

{% asset_img chartjs.png %}

chart.js is a library that provides various charts, the project renders charts and the data is fetched from Rest API.

{% ghcode https://github.com/gitorko/project79/blob/master/src/main/resources/templates/charts.html 16 23 %}


{% ghcode https://github.com/gitorko/project79/blob/master/src/main/resources/static/js/charts.js 23 52 %}

## Thymeleaf

{% asset_img thymeleaf.png %}

We will use thymeleaf fragments to include menu headers.

{% ghcode https://github.com/gitorko/project79/blob/master/src/main/resources/templates/home.html 3 9 %}

## Bootstrap 5

{% asset_img bootstrap.png %}

We will use the bootstrap 5 library and use the many components it provides.

{% ghcode https://github.com/gitorko/project79/blob/master/src/main/resources/templates/login.html 10 38 %}

## HSQL DB

{% asset_img hsql.png %}

To start the HSQLDB server
1. Download HSQLDB bundle
   [http://hsqldb.org/](http://hsqldb.org/)
   [https://sourceforge.net/projects/hsqldb/files/hsqldb/hsqldb_2_6/hsqldb-2.6.0.zip/download](https://sourceforge.net/projects/hsqldb/files/hsqldb/hsqldb_2_6/hsqldb-2.6.0.zip/download)
2. Run the below command to create the db.
```bash
cd hsqldb-2.6.0/hsqldb
java -cp lib/hsqldb.jar org.hsqldb.server.Server --database.0 file:data/mydb --dbname.0 mydb
```
3. To connect to the DB via HSQL UI
```bash   
java -cp lib/hsqldb.jar org.hsqldb.util.DatabaseManagerSwing
jdbc:hsqldb:hsql://localhost:9001/mydb
user: SA
pwd:
```

## Build

Run the project on dev

```bash
./gradlew bootRun
```

To build the fat jar & run the jar.

```bash
./gradlew build
java -jar project79-1.0.0.jar
```

[http://localhost:8080/](http://localhost:8080/)
user: admin
pwd: admin@123

## Screenshots

Here are some screenshots of the web application

{% asset_img image02.png %}
{% asset_img image03.png %}
{% asset_img image04.png %}


## References

Bootstarp 5 : [https://getbootstrap.com/](https://getbootstrap.com/)
Chart.js : [https://www.chartjs.org/](https://www.chartjs.org/)
