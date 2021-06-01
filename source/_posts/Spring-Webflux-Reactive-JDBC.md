---
title: Spring Webflux & Reactive JDBC
date: 2019-04-02 00:00:00
tags: 
- webflux
- reactive-jdbc
categories:
- [JPA]
- [SpringBoot]
---

A Webflux application integration with JDBC to allow non-blocking calls to database. R2DBC is still not production ready hence this approach should help you integrate existing relational database with webflux.

Github: [https://github.com/gitorko/project64](https://github.com/gitorko/project64)

<!-- more -->

<!-- toc -->

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

## Webflux Application

Spring webflux provided non-blocking support for rest api. However, with R2DBC still under experimental support the following approach provides a way to make non-blocking calls to JDBC.

{% ghcode https://github.com/gitorko/project64/blob/master/src/main/java/com/demo/reactdb/project64/Application.java %}

{% ghcode https://github.com/gitorko/project64/blob/master/src/main/resources/application.yaml %}

Run the project

```bash
./gradlew bootRun
```

Your service should now be up

```bash
curl -X GET \
  http://localhost:8080/api/all \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 03d44176-e636-4174-a0da-b2286a3d580d' \
  -H 'cache-control: no-cache'

curl -X GET \
  http://localhost:8080/api/id/1 \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 7649705c-8b3e-4c4d-b222-fafeddcbcc1c' \
  -H 'cache-control: no-cache'

curl -X POST \
  http://localhost:8080/api/save \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 765e1c69-4328-4ff4-9418-58f9cb6d4caa' \
  -H 'cache-control: no-cache' \
  -d '{
    "name": "david",
    "age": 25
}'
```
