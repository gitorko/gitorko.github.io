---
title: Spring Boot Web MVC
date: 2021-05-22 00:00:00
tags:
- web mvc 
- chart.js
- CRUD & login
- HSQL DB 
- Thymeleaf template  
- Bootstrap 5
- Spring security login
categories:
- [Web]
- [Spring]
---

A Spring boot web application with a basic spring security & bootstrap login page and CRUD operations along with chart.js charts

Github: [https://github.com/gitorko/project79](https://github.com/gitorko/project79)

<!-- more -->

<!-- toc -->

## MVC Web Application

The project provides a template to build more complex web application that would require a basic login logout, it supports chart.js charts that read data from Rest API.
Spring dev tools allow seamless reload on any changes to html and java files.

1. Supports basic login via spring security
2. Bootstrap 5
3. Login screen
4. CRUD UI for adding and removing customer
5. HSQL db
6. Spring JPA
7. Thymeleaf template
8. Chart.js charts for bar,pie,stack charts with data from rest api

On intellij to allow spring dev tools to reload on change you need to enable 'Update classes and resources' as shown below
{% asset_img image01.png %}

Here are some screenshots of the web application

{% asset_img image02.png %}
{% asset_img image03.png %}
{% asset_img image04.png %}

## HSQL DB server
To start the HSQLDB server
1. Download HSQLDB bundle 
   [http://hsqldb.org/](http://hsqldb.org/) 
   [https://sourceforge.net/projects/hsqldb/files/hsqldb/hsqldb_2_6/hsqldb-2.6.0.zip/download](https://sourceforge.net/projects/hsqldb/files/hsqldb/hsqldb_2_6/hsqldb-2.6.0.zip/download)
2. Run the below command to create the db.
```bash   
java -cp lib/hsqldb.jar org.hsqldb.server.Server --database.0 file:data/mydb --dbname.0 mydb
```
3. To connect to the DB via HSQL UI
```bash   
java -cp lib/hsqldb.jar org.hsqldb.util.DatabaseManagerSwing
jdbc:hsqldb:hsql://localhost:9001/mydb
user: SA
pwd:
```

Run the project

```bash
./gradlew bootRun
```

## References

Bootstarp 5 : [https://getbootstrap.com/](https://getbootstrap.com/)
Chart.js : [https://www.chartjs.org/](https://www.chartjs.org/)
