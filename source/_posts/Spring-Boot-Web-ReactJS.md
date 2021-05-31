---
title: Spring Boot Web - ReactJS
date: 2021-05-25 00:00:00
tags:
- spring web
- reactjs
- chart.js
- spring jpa
- hsql db
- bootstrap 5
- spring security
- jwt
categories:
- [Web]
- [Spring]
- [ReactJS]
---

A Spring boot web application with reactjs and basic JWT authentication support, uses bootstrap for CSS and chart.js for rendering charts. Creates uber jar to deploy.

Github: [https://github.com/gitorko/project80](https://github.com/gitorko/project80)

<!-- more -->

<!-- toc -->

## Spring Web

{% asset_img spring.png %}

A Spring Web application with reactjs. Supports basic integration with spring JWT security and provides login & logout support.
Uses Spring Data to persist data into the HSQL db. A file based HSQL server db is used so that data persists across restarts. This can easily be changed to in-memory HSQL db.
Spring dev tools allow seamless reload on any changes for java files.

Features:

1. ReactJS app supports basic login via JWT
2. Bootstrap 5
3. JWT token based Login
4. CRUD UI for adding and removing customer
5. HSQL db
6. Spring JPA
7. Chart.js charts for bar,pie,stack charts with data from rest api

On Intellij to allow spring dev tools to reload on change you need to enable 'Update classes and resources' as shown below
{% asset_img image01.png %}

The code is split into 2 different projects so that UI and backend can work independently. In the dev environment backend will run via `./gradlew bootRun` and frontend will run via `yarn start`
Finally you can bundle frontend(reactjs) & backend into a single uber jar that can be deployed.

Rest API return data that is rendered in reactjs frontend.

{% ghcode https://raw.githubusercontent.com/gitorko/project80/master/project80-svc/src/main/java/com/demo/project80/controller/HomeController.java 31 34 %}

To ensure that all url request related to UI get redirected to the index.html file of reactjs the following controller is created. Only rest api with `/api` get served by the rest controllers. This only matters when you bundle everything into a uber jar where the spring jar serves both the static files and the rest api.

{% ghcode https://raw.githubusercontent.com/gitorko/project80/master/project80-svc/src/main/java/com/demo/project80/controller/IndexController.java 11 14 %}

Spring security is configured for JWT authentication.

{% ghcode https://raw.githubusercontent.com/gitorko/project80/master/project80-svc/src/main/java/com/demo/project80/config/WebSecurity.java 29 40 %}

## chart.js

{% asset_img chartjs.png %}

chart.js is a library that provides various charts, the project renders charts and the data is fetched from Rest API.

{% ghcode https://raw.githubusercontent.com/gitorko/project80/master/project80-ui/src/Chart.js 190 197 %}


{% ghcode https://raw.githubusercontent.com/gitorko/project80/master/project80-ui/src/Chart.js 107 119 %}

## ReactJS

{% asset_img reactjs.png %}

We will use ReactJS for the frontend.

{% ghcode https://raw.githubusercontent.com/gitorko/project80/master/project80-ui/src/services/RestService.js 26 37 %}

## Bootstrap 5

{% asset_img bootstrap.png %}

We will use the bootstrap 5 library and use the many components it provides.

{% ghcode https://raw.githubusercontent.com/gitorko/project80/master/project80-ui/src/Login.js 62 77 %}

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
cd project80-svc
./gradlew bootRun

cd project80-ui
./gradlew yarn_install
./gradlew yarn_start
```

[http://localhost:3000/](http://localhost:3000/)
user: admin
pwd: admin@123

To build the fat jar & run the jar. Build the UI project prior to building the service project as the dist is copied into the spring uber jar.

```bash
cd project80-ui
./gradlew yarn_build

cd project80-svc
./gradlew build
java -jar project80-svc-1.0.0.jar
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

Login page
{% asset_img image02.png %}

Home page
{% asset_img image03.png %}

Charts page
{% asset_img image04.png %}

## References

Bootstarp 5 : [https://getbootstrap.com/](https://getbootstrap.com/)
Chart.js : [https://www.chartjs.org/](https://www.chartjs.org/)
