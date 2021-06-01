---
title: Spring Boot Web - Angular
date: 2021-05-25 00:00:00
tags:
- spring web
- angular 11
- chart.js
- spring jpa
- hsql db
- clarity
- spring security
- jwt
categories:
- [Web]
- [Spring]
- [Angular]
- [Clarity]
---

A Spring boot web application with angular and basic JWT authentication support, uses clarity for UI components and chart.js for rendering charts. Creates uber jar to deploy.

Github: [https://github.com/gitorko/project81](https://github.com/gitorko/project81)

<!-- more -->

<!-- toc -->

## Spring Web

{% asset_img spring.png %}

A Spring Web application with angular 11. Supports basic integration with spring JWT security and provides login & logout support.
Uses Spring Data to persist data into the HSQL db. A file based HSQL server db is used so that data persists across restarts. This can easily be changed to in-memory HSQL db.
Spring dev tools allow seamless reload on any changes for java files.

Features:

1. Angular 11 app supports basic login via JWT
2. Clarity
3. JWT token based Login
4. CRUD UI for adding and removing customer
5. HSQL db
6. Spring JPA
7. Chart.js charts for bar,pie,stack charts with data from rest api

On Intellij to allow spring dev tools to reload on change you need to enable 'Update classes and resources' as shown below
{% asset_img image01.png %}

The code is split into 2 different projects so that UI and backend can work independently. In the dev environment backend will run via `./gradlew bootRun` and frontend will run via `ng serve --proxy-config proxy.config.json --open`
Finally you can bundle frontend(angular) & backend into a single uber jar that can be deployed.

Rest API return data that is rendered in angular frontend.

{% ghcode https://github.com/gitorko/project81/blob/master/project81-svc/src/main/java/com/demo/project81/controller/HomeController.java 31 34 %}

To ensure that all url request related to UI get redirected to the index.html file of angular app the following controller is created. Only rest api with `/api` get served by the rest controllers. This only matters when you bundle everything into a uber jar where the spring jar serves both the static files and the rest api.

{% ghcode https://github.com/gitorko/project81/blob/master/project81-svc/src/main/java/com/demo/project81/controller/IndexController.java 11 14 %}

Spring security is configured for JWT authentication.

{% ghcode https://github.com/gitorko/project81/blob/master/project81-svc/src/main/java/com/demo/project81/config/WebSecurity.java 26 36 %}

## chart.js

{% asset_img chartjs.png %}

chart.js is a library that provides various charts, the project renders charts and the data is fetched from Rest API.

{% ghcode https://github.com/gitorko/project81/blob/master/project81-ui/src/app/chart/chart.component.ts 119 124 %}


{% ghcode https://github.com/gitorko/project81/blob/master/project81-ui/src/app/chart/chart.component.html 3 8 %}

## Angular

{% asset_img angular.png %}

We will use Angular framework for the frontend.

{% ghcode https://github.com/gitorko/project81/blob/master/project81-ui/src/app/app-routing.module.ts 8 22 %}

## Clarity

{% asset_img clarity.png %}

We will use the clarity component library for the many components it provides.

{% ghcode https://github.com/gitorko/project81/blob/master/project81-ui/src/app/home/home.component.html 72 86 %}

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

Start the HSQL DB server.
Run the project on dev.

```bash
cd project81-svc
./gradlew bootRun

cd project81-ui
ng build
ng serve --proxy-config proxy.config.json --open
```

[http://localhost:4200/](http://localhost:4200/)
user: admin
pwd: admin@123

To build the fat jar & run the jar. Build the UI project prior to building the service project as the dist is copied into the spring uber jar.

```bash
cd project81-ui
ng build

cd project81-svc
./gradlew build
java -jar project81-svc-1.0.0.jar
```

[http://localhost:8080/](http://localhost:8080/)
user: admin
pwd: admin@123

```bash
curl --location --request POST 'http://localhost:8080/api/login' \
--header 'Content-Type: application/json' \
--data-raw '{
    "username": "admin",
    "password": "admin@123"
}'
```


```bash
curl --location --request GET 'http://localhost:8080/api/time' \
--header 'Authorization: Bearer TOKEN'
```

## Screenshots

Here are some screenshots of the web application

Login screen
{% asset_img image02.png %}

Home page
{% asset_img image03.png %}

Charts page
{% asset_img image04.png %}

## References

Clarity : [https://clarity.design/](https://clarity.design/)
Chart.js : [https://www.chartjs.org/](https://www.chartjs.org/)
