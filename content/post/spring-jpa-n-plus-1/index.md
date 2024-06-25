---
title: 'Spring JPA N+1'
description: 'Spring JPA N+1'
summary: 'The N+1 query problem occurs when the framework executes N additional SQL statements to fetch the same data that could have been retrieved when executing the primary SQL query.'
date: '2019-06-11'
aliases: [/spring-jpa-n-plus-1/]
author: 'Arjun Surendra'
categories: [Spring, JPA]
tags: [spring, jpa, h2]
toc: true
---

The N+1 query problem occurs when the framework executes N additional SQL statements to load lazily fetched objects, this happens when you use FetchType.LAZY for your entity associations.

Github: [https://github.com/gitorko/project66](https://github.com/gitorko/project66)

## N+1 problem

By default, fetch is `FetchType.LAZY` in hibernate, changing to `FetchType.EAGER` won't guarantee a fix for N+1 issue either, eager fetch will fetch more data than needed.
The `@ManyToOne` and `@OneToOne` associations use `FetchType.EAGER` by default.

**Different ways to solve N+1 Problem**

1. `FetchType.EAGER` - Fetches more data that you need
2. Join fetch - joins the two tables & initializes the objects. It works with both JOIN and LEFT JOIN statements.
3. JPA EntityGraphs - allows partial or specified fetching of objects, specify a fetch plan by EntityGraphs in order to determine which fields or properties should be fetched together
4. Batching - `@BatchSize(size = 10)`
5. Subselect - `@Fetch(FetchMode.SUBSELECT)`
5. Spring Data JDBC supports Single Query Loading

**Fetch Graph vs Load Graph**

There are two types of EntityGraphs, Fetch and Load, they define if the entities not specified by attributeNodes of EntityGraphs should be fetched lazily or eagerly.

1. FETCH - default graph type. When it is selected, the attributes that are specified by attribute nodes of the entity graph are treated as FetchType.EAGER and attributes that are not specified are treated as FetchType.LAZY
2. LOAD - attributes that are specified by attribute nodes of the entity graph are treated as FetchType.EAGER

More complex and reusable graphs we can describe a fetch plan with its paths and boundaries with @NamedEntityGraph annotation in the entity class.

### Code

N+1 query that executes N times

```sql
select c1_0.post1_id,c1_1.id,c1_1.comment from "post1_comments" c1_0 join "post-comment1" c1_1 on c1_1.id=c1_0."comments_id" where c1_0.post1_id=?
```

Sql with left join

```sql
select p1_0.id,c1_0.post1_id,c1_1.id,c1_1.comment,p1_0.title from post1 p1_0 left join "post1_comments" c1_0 on p1_0.id=c1_0.post1_id left join "post-comment1" c1_1 on c1_1.id=c1_0."comments_id"
```

Sql with join
```sql
select p1_0.id,c1_0.post1_id,c1_1.id,c1_1.comment,p1_0.title from post1 p1_0 join "post1_comments" c1_0 on p1_0.id=c1_0.post1_id join "post-comment1" c1_1 on c1_1.id=c1_0."comments_id"
```

Entity Graph

```sql
 select p1_0.id,c1_0.post4_id,c1_1.id,c1_1.comment,p1_0.title from post4 p1_0 left join "post4_comments" c1_0 on p1_0.id=c1_0.post4_id left join "post-comment4" c1_1 on c1_1.id=c1_0."comments_id"
```

Batch

```sql
select c1_0.post2_id,c1_1.id,c1_1.comment from "post2_comments" c1_0 join "post-comment2" c1_1 on c1_1.id=c1_0."comments_id" where c1_0.post2_id in (?,?,?,?,?,?,?,?,?,?)
```

Sub-Select

```sql
select c1_0.post3_id,c1_1.id,c1_1.comment from "post3_comments" c1_0 join "post-comment3" c1_1 on c1_1.id=c1_0."comments_id" where c1_0.post3_id in (select p1_0.id from post3 p1_0)
```

{{< ghcode "https://raw.githubusercontent.com/gitorko/project66/main/src/main/java/com/demo/project66/Main.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project66/main/src/main/resources/application.yaml" >}}

### Postman

Import the postman collection to postman

[Postman Collection](https://raw.githubusercontent.com/gitorko/project66/main/postman/Project66.postman_collection.json)

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project66/main/README.md" >}}

## References

[https://vladmihalcea.com/n-plus-1-query-problem](https://vladmihalcea.com/n-plus-1-query-problem)
