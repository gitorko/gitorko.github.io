---
title: 'Spring - Apache Ignite'
description: 'Spring - Apache Ignite'
summary: 'Spring - Apache Ignite'
date: '2024-06-15'
aliases: [/spring-apache-ignite/]
author: 'Arjun Surendra'
categories: [ApacheIgnite, Spring]
tags: [ignite, caching, postgres]
toc: true
---

Spring boot application with apache ignite integration

Github: [https://github.com/gitorko/project91](https://github.com/gitorko/project91)

## Apache Ignite

Apache Ignite is a distributed database. 
Data in Ignite is stored in-memory and/or on-disk, and is either partitioned or replicated across a cluster of multiple nodes. 

Features:

1. Store data in key-value pair
2. Supports caching by storing data in-memory
3. Supports on disk storage
4. Supports ACID transactions (only at the key-value level)
5. Supports RDMS like SQL queries, Does not support foreign key constraints
6. In-Memory data grid
7. Supports stream processing
8. Supports distributed compute
9. Supports scaling & resiliency
10. Supports messaging queue
11. Supports Multi-tier storage

Apache Ignite Setup

1. Embedded server
2. Embedded client 
3. Cluster setup

Apache Ignite automatically synchronizes the changes with the database in an asynchronous, background task
If an entity is not cached it is read from the database and put to the cache for future use.

### Redis vs Apache Ignite

While Redis stores data in memory, Ignite relies on memory and disk to store data. Hence, Ignite can store much larger amounts of data than Redis

### EhCache vs Apache Ignite
Ehcache is more focused on local caching and does not provide built-in support for distributed caching or computing. EhCache primarily intended for single-node caching scenarios.


1. Scalability and Distributed Computing - Easily scaled across multiple nodes in a cluster. It allows for data and computation to be distributed across the nodes, providing high availability and fault tolerance.
2. Data Partitioning and Replication: - Offers advanced data partitioning and replication capabilities. It automatically partitions the data across multiple nodes in a cluster, ensuring that each node only holds a portion of the overall data set. This enables parallel processing and efficient data retrieval. In addition, Ignite allows for configurable data replication, ensuring data redundancy and fault tolerance. 
3. Computational Capabilities - Supports running distributed computations across the cluster, allowing for parallel processing and improved performance. It provides APIs for distributed SQL queries, machine learning, and real-time streaming analytics.
4. Integration with Other Technologies: Integrates seamlessly with various other technologies and frameworks. It provides connectors and integrations for popular data processing frameworks like Apache Spark, Apache Hadoop, and Apache Cassandra. It also offers support for various persistence stores, such as JDBC, NoSQL databases, and Hadoop Distributed File System (HDFS).
5. Transaction Support: Supports distributed transactions, allowing multiple nodes in a cluster to participate in a single transaction. It ensures consistency and isolation across the distributed cache.
6. Management and Monitoring Capabilities: Offers a web-based management console for monitoring the cluster status, metrics, and performance.


### Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project91/main/src/main/java/com/demo/project91/config/IgniteConfig.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project91/main/src/main/java/com/demo/project91/config/SpringCacheConfig.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project91/main/src/main/java/com/demo/project91/config/DbFactory.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project91/main/src/main/java/com/demo/project91/service/CustomerService.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project91/main/src/main/java/com/demo/project91/service/EmployeeService.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project91/main/src/main/java/com/demo/project91/service/CompanyService.java" >}}


### Issues

Standard CrudRepository save(entity), save(entities), delete(entity) operations aren't supported.
We have to use the save(key, value), save(Map<ID, Entity> values), deleteAll(Iterable<ID> ids) methods.

@EnableIgniteRepositories declared on IgniteConfig: Can not perform the operation because the cluster is inactive. Note, that the cluster is considered inactive by default if Ignite Persistent Store is used to let all the nodes join the cluster. 
To activate the cluster call Ignite.cluster().state(ClusterState.ACTIVE).

```
Possible too long JVM pause: 418467 milliseconds.
Blocked system-critical thread has been detected. This can lead to cluster-wide undefined behaviour
```

GC pauses decreases overall performance. if pause will be longer than failureDetectionTimeout node will be disconnected from cluster.
[https://apacheignite.readme.io/docs/jvm-and-system-tuning](https://apacheignite.readme.io/docs/jvm-and-system-tuning)

```
 Failed to add node to topology because it has the same hash code for partitioned affinity as one of existing nodes
```

Instance cant have same node id.

When 3 nodes are running you will see the cluster

```
Topology snapshot [ver=3, locNode=2e963fb3, servers=3, clients=0, state=ACTIVE, CPUs=16, offheap=38.0GB, heap=24.0GB]
```

```
Failed to validate cache configuration. Cache store factory is not serializable.
```
CacheJdbcPojoStoreFactory will be serialized hence needs to implement Serializable

### References

[https://ignite.apache.org/](https://ignite.apache.org/)