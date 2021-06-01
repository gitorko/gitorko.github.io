---
title: Spring Webflux & R2DBC
date: 2019-04-03 00:00:00
tags: 
- webflux
- r2dbc
categories:
- [JPA]
- [SpringBoot]
---

A Webflux application integration with reactive R2DBC. R2DBC stands for Reactive Relational Database Connectivity, It provides a reactive driver to connect to relational database. Spring Data R2DBC makes it easy to use R2DBC libraries.

Github: [https://github.com/gitorko/project65](https://github.com/gitorko/project65)

<!-- more -->

<!-- toc -->

## Spring Data R2DBC

Spring Data R2DBC aims at being conceptually easy. In order to achieve this it does NOT offer caching, lazy loading, write behind or many other features of ORM frameworks. This makes Spring Data R2DBC a simple, limited, opinionated object mapper. Currently its use in production is not recommended. 

As of this writing only 3 relational database have r2dbc libraries
  * Postgres (io.r2dbc:r2dbc-postgresql)
  * H2 (io.r2dbc:r2dbc-h2)
  * Microsoft SQL Server (io.r2dbc:r2dbc-mssql)

## Postgres Docker Instance
For development activities lets bring up a postgres server as a docker container. 

```bash
docker run -d --name postgres -p 5432:5432 -e POSTGRES_USER="demouser" -e POSTGRES_PASSWORD="demopwd" -e POSTGRES_DB="demodb" postgres
```

We will now connect to the db and create the table.

```bash
docker exec -it postgres psql -U demouser demodb
```
Run the sql

```sql
CREATE TABLE customer (
   id  SERIAL PRIMARY KEY,
   name VARCHAR(50) NOT NULL,
   AGE INT NOT NULL
);
```

You should be able to see your table.
```bash
demodb=# \dt
          List of relations
 Schema |   Name   | Type  |  Owner   
--------+----------+-------+----------
 public | customer | table | demouser
(1 row)
```

## Webflux application

{% ghcode https://github.com/gitorko/project65/blob/master/src/main/java/com/demo/project65/Application.java %}

Run the project

```bash
./gradlew bootRun
```

Your service should now be up

```bash
curl -X GET \
  'http://localhost:8080/api/find?name=raj&age=25' \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 0a64b5ab-9725-41d6-8103-f7a5b5757931' \
  -H 'cache-control: no-cache'

curl -X GET \
  http://localhost:8080/api/all \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 86d0427e-5474-4d65-8f97-02dec783af1a' \
  -H 'cache-control: no-cache'

curl -X GET \
  http://localhost:8080/api/id/1 \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: b7c57690-beb7-437f-b8b8-3f15d7389feb' \
  -H 'cache-control: no-cache'

curl -X POST \
  http://localhost:8080/api/save \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: b4ba9015-e74f-43eb-87c6-2efc1711b3f7' \
  -H 'cache-control: no-cache' \
  -d '{
    "name": "jack",
    "age": 33
}'
```

## Errors
If you encounter any of the error mentioned below it could probably be because the data type in postgres cant be mapped by r2dbc.
Eg: CHAR is not supported, changing to VARCHAR will fix the issue.

org.springframework.data.mapping.MappingException: Could not read property public java.lang.String com.demo.project65.Customer.name from result set!
org.springframework.data.r2dbc.function.convert.EntityRowMapper.readFrom(EntityRowMapper.java:103) ~[spring-data-r2dbc-1.0.0.M1.jar:1.0.0.M1]
Caused by: java.lang.IllegalArgumentException: Cannot decode value of type java.lang.Object
org.springframework.data.r2dbc.function.convert.EntityRowMapper.readFrom(EntityRowMapper.java:99) ~[spring-data-r2dbc-1.0.0.M1.jar:1.0.0.M1]
