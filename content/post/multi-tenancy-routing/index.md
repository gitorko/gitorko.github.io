---
title: 'Spring & Postgres - Multi-Tenancy & Routing'
description: 'Spring & Postgres - Multi-Tenancy & Routing'
summary: 'Spring & Postgres - Multi-Tenancy & Routing'
date: '2024-07-28'
aliases: [/alias/]
author: 'Arjun Surendra'
categories: [Spring, JPA]
tags: [jdbc, multi-tenancy, liquibase]
toc: true
draft: false
---

Spring JPA implementation with multi-tenancy and routing.

Github: [https://github.com/gitorko/project101](https://github.com/gitorko/project101)

## Postgres Multi-Tenancy

Multi-tenancy is an architectural pattern that allows you to isolate customers even if they are using the same hardware or software components.

1. Catalog-based - Each region gets its own database
2. Schema-based - Single database but different schema for each region
3. Table-based - Single database, single table but a column identifies the region.

There are 2 approache to implement multi-tenancy

1. AbstractMultiTenantConnectionProvider - Handles Hibernate session factory connections for different tenants.
2. AbstractRoutingDataSource - Handles other aspects of data source routing in the application, such as switching data sources for non-Hibernate use cases.

### Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project101/main/src/main/java/com/demo/project101/config/DataSourceConfig.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project101/main/src/main/java/com/demo/project101/config/LiquibaseConfig.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project101/main/src/main/java/com/demo/project101/config/RoutingConfig.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project101/main/src/main/java/com/demo/project101/config/TenantContext.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project101/main/src/main/java/com/demo/project101/controller/CustomerController.java" >}}

### Postman

Import the postman collection to postman

[Postman Collection](https://raw.githubusercontent.com/gitorko/project101/main/postman/Project101.postman_collection.json)

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project101/main/README.md" >}}

## References

[https://spring.io](https://spring.io/)

[https://vladmihalcea.com/database-multitenancy/](https://vladmihalcea.com/database-multitenancy/)

[https://vladmihalcea.com/hibernate-database-schema-multitenancy/](https://vladmihalcea.com/hibernate-database-schema-multitenancy/)

[https://vladmihalcea.com/read-write-read-only-transaction-routing-spring/](https://vladmihalcea.com/read-write-read-only-transaction-routing-spring/)
