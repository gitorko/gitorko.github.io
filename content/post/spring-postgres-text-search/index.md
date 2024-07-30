---
title: 'Spring Boot & Postgres - Text Search'
description: 'Spring Boot & Postgres - Text Search'
summary: 'Spring Boot & Postgres - Text Search'
date: '2024-07-01'
aliases: [/spring-postgres-text-search/]
author: 'Arjun Surendra'
categories: [Spring, JPA, Postgres]
tags: [jdbc, text-search, liquibase]
toc: true
draft: false
---

Spring boot application with postgres text search implementation

Github: [https://github.com/gitorko/project103](https://github.com/gitorko/project103)

## Text Search

Using `like` keyword for search is not efficient for text search. There is no ranking and no indexes can be used.

```sql
select * from customer where description like '%play%';
```

Elastic search can also be used to search text for large scale. For simpler small scale text search you can use postgres and leverage existing database.

1. to_tsvector - Will remove stop words, find lexical words, adds positions
2. to_tsquery - Will search the tsvector

`gin` - generalized inverted index will be created to search.

SQL queries

```sql
select description::tsvector
from customer;

select to_tsvector(description)
from customer;

select to_tsquery('Loves')
from customer;

select websearch_to_tsquery('Loves and Skating')
from customer;

select to_tsvector(description) @@ websearch_to_tsquery('Loves and Skating')
from customer;

select *
from customer
where to_tsvector(name || ' ' || coalesce(description, '')) @@ websearch_to_tsquery('Loves and Skating');

select *, ts_rank(to_tsvector(name || ' ' || coalesce(description, '')), websearch_to_tsquery('Loves and Skating')) as rank
from customer
where to_tsvector(name || ' ' || coalesce(description, '')) @@ websearch_to_tsquery('Loves and Skating')
order by rank desc;
```
### Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project103/main/src/main/resources/db/changelog/changes/001-create-schema.sql" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project103/main/src/main/java/com/demo/project103/repository/CustomerRepository.java" >}}

### Postman

Import the postman collection to postman

[Postman Collection](https://raw.githubusercontent.com/gitorko/project103/main/postman/Project103.postman_collection.json)

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project103/main/README.md" >}}

## References

[https://www.postgresql.org/docs/current/textsearch.html](https://www.postgresql.org/docs/current/textsearch.html)
