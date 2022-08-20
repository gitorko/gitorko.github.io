---
title: 'Apache Superset'
description: 'Apache Superset'
summary: 'Apache Superset is a modern data exploration and visualization platform.'
date: '2021-11-11'
aliases: [/apache-superset/]
author: 'Arjun Surendra'
categories: [Visualization]
tags: [apache-superset,visualization,postgres]
toc: true
---

Apache Superset is a modern data exploration and visualization platform.

Github: [https://github.com/gitorko/project93](https://github.com/gitorko/project93)

![](superset.png)

We will use a postgres db with a sample db and then create some charts around it in apache superset.

## Postgres

Setup postgres & seed the employee database with some data.

```bash
docker run -p 5432:5432 --name pg-container -e POSTGRES_PASSWORD=password -d postgres:9.6.10
docker ps
docker exec -it pg-container psql -U postgres -W postgres
CREATE USER test WITH PASSWORD 'test@123';
CREATE DATABASE "test-db" WITH OWNER "test" ENCODING UTF8 TEMPLATE template0;
GRANT ALL PRIVILEGES ON DATABASE "test-db" to test;

docker stop pg-container
docker start pg-container
```

```bash
docker exec -i pg-container psql -U postgres < employees.sql
docker exec -it pg-container psql -U postgres -W postgres
\c test-db;
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO "test";
```

```bash
pwd: password
```

Download the employee.sql file [https://github.com/gitorko/project93/blob/main/employees.sql.zip](https://github.com/gitorko/project93/blob/main/employees.sql.zip)

## Apache Superset

Create the Dockerfile. Pick the python database driver you need to install. In this case its postgres.

{% ghcode https://github.com/gitorko/project93/blob/main/Dockerfile %}

Run the following commands to bring up the superset server

```bash
docker build -t superset-image .
docker run -d -p 8080:8088 --name my-superset superset-image
docker exec -it my-superset superset fab create-admin \
               --username admin \
               --firstname Superset \
               --lastname Admin \
               --email admin@superset.com \
               --password admin
docker exec -it my-superset superset db upgrade
docker exec -it my-superset superset init
```

If the docker build fails during pip install, update the dns configuration on docker & restart docker

![](img09.png)

Login to the server

[http://localhost:8080/login/](http://localhost:8080/login/)

```bash
user: admin
pwd: admin
```

You can also load sample database and charts 

```bash
docker exec -it my-superset superset load_examples
```

For the next restart you just need to start the container

```bash
docker start my-superset
```

![](img01.png)

Goto Data -> Databases and add the postgres db. Ensure to give the IP address of the machine and not localhost.

```bash
postgresql://test:test@123@10.104.66.186:5432/test-db
```

![](img02.png)

Click on datasets and add the tables.

![](img03.png)

![](img04.png)

Create a new chart, save the chart and create a dashboard from the chart.

![](img05.png)

![](img06.png)

![](img07.png)

![](img08.png)


## References

[https://hub.docker.com/r/apache/superset](https://hub.docker.com/r/apache/superset)

[https://superset.apache.org/](https://superset.apache.org/)
