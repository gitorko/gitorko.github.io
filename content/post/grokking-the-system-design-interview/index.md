---
title: 'Grokking the System Design Interview'
description: 'Grokking the System Design Interview'
summary: 'Interview preparation guide for system design'
date: '2022-01-17'
aliases: [/grokking-the-system-design-interview/]
author: 'Arjun Surendra'
categories: [System-Design]
tags: [interview, system-design]
toc: true
---

## System Design

We will understand the fundamentals of system design and look at real world implementations 

System design interview discussions are of two types

## Wide design (HLD - High Level Design)

Wide design/HLD often is very open-ended and broad without any specifics.  

Eg: Design Food Delivery App, Design Uber, Design Twitter.

You often end up explaining the following

1. Component diagrams
2. Class diagrams
3. Sequence diagrams & Flow Diagrams
4. Decision Trees
5. Use-cases
6. Database Schema & ER diagrams
7. API

Prior knowledge of the product is required to some extent. If the candidate is asked to design `management system for a particle accelerator` without knowledge of the system it becomes very difficult.

## Deep design (LLD - Low Level Design)

Deep design/LLD often starts with a specific module with a clear outcome that is expected, and provides you more details about the problem statement and the expected output.

Eg: Design a build system that builds the artifact when code is submitted, design a tiny url service etc.

The outcome of a design should be such that if handed to another developer, he should be able to implement it.

### 1. Functional requirements

* What is the start & end result/state?
* Is this is a **live** service or a **background** service?
* Does this need stream processing or batch processing?
* How **many users**?
* Does the data need to be **persisted**?
* Does the data need to be **consistent** or can it be eventually consistent?
* Does the service need external **API** 

### 2. Non-Functional requirements

* What is the **latency** expected?
* What is **load** the system needs to handle and **scale** requirements?
* What **observability** & **monitoring** is needed by the system?
* What is the uptime & **availability** SLA?
* What **security** aspects need to be addressed?
* Does it need auditing?

### 3. Capacity planning

* How many servers would you need?
* How much storage is required?
* What bandwidth is required?
* Do you need GPU specific processors or CPU specific processors?
* What time of the day do you need the servers?

## Fundamentals

### 1. Short-Polling vs Long-Polling vs SSE (Server Sent Events) vs Websocket

1. Short-Polling - Client continuously asks the server for new data.
2. Long-Polling - Client continuously asks the server for new data, but server waits for a few seconds and if data becomes available by then it will return the data.
3. Websocket - HTTP connection is upgraded to bidirectional connection.
4. Server Sent Events - HTTP connection is kept open by the server and data is pushed to client continuously over it.

| Websocket                 | Server Sent Event          | Long-Poll                  |
|:--------------------------|:---------------------------|:---------------------------|
| Full-duplex,Bidirectional | Half-duplex,Unidirectional | Half-duplex,Unidirectional |
| Server Push & Client Send | Server Push                | Client Pull                |
| Text + Binary             | Text                       | Text + Binary              |
| 1024 connections          | 6-8 parallel per domain    | Based on threads available |

[https://youtu.be/ZBM28ZPlin8](https://youtu.be/ZBM28ZPlin8)

### 2. Fork Join Pool

Fork Join is suited for tasks that create sub-tasks. Fork/Join framework uses work-stealing algorithm.
Work stealing is a scheduling strategy where worker threads that have finished their own tasks can steal pending tasks from other threads.
Uses a deque (double ended queue), main thread picks task from the front of the queue, other threads steal tasks from the back of the queue.

```java
@RequiredArgsConstructor
 class FibForkJoin extends RecursiveTask<Integer> {
     final int n;

     @Override
     protected Integer compute() {
         System.out.println("Current Thread: " + Thread.currentThread().getName() + " n = " + n);
         if (n <= 1) {
             return n;
         }
         FibForkJoin f1 = new FibForkJoin(n - 1);
         f1.fork();
         FibForkJoin f2 = new FibForkJoin(n - 2);
         f2.fork();
         return f1.join() + f2.join();
     }
 }
```

[https://youtu.be/5wgZYyvIVJk](https://youtu.be/5wgZYyvIVJk)

### 3. Distributed Transaction

The best thing to do is completely avoid distributed transactions. As it makes the system complex to manage. However, if that is not possible.

1. Two phase (prepare & commit) - Blocking protocol as it waits for the prepare-ack for prepare phase.
2. Three phase commit (prepare, pre-commit & commit) - Non-Blocking protocol as first phase gathers votes and only the second phase blocks with timeout.

![](distributed-transaction.png)

[https://youtu.be/jGJT1FRYGcY](https://youtu.be/jGJT1FRYGcY)

[https://youtu.be/S4FnmSeRpAY](https://youtu.be/S4FnmSeRpAY)

### 4. Saga

A saga is a sequence of local transactions.
Each local transaction updates the database and publishes a message or event to trigger the next local transaction in the saga.
If a local transaction fails because it violates a business rule then the saga executes a series of compensating transactions that undo the changes that were made by the preceding local transactions.

The 2 approaches

1. Choreography - Each local transaction publishes domain events that trigger local transactions in other services.
2. Orchestration - An orchestrator tells the participants what local transactions to execute.

Problems with saga

1. Hard to debug & test.
2. Risk of cyclic dependency between saga participants.

### 5. Isolation Levels

1. Dirty reads: read UNCOMMITED data from another transaction.
2. Non-repeatable reads: read COMMITTED data from an UPDATE query from another transaction.
3. Phantom reads: read COMMITTED data from an INSERT or DELETE query from another transaction.

Dirty Read

| NAME  | AGE |
|:------|:----|
| Bob   | 35  |

| TRANSACTION T1                                 | TRANSACTION T2                                |
|:-----------------------------------------------|:----------------------------------------------|
| select age from table where name = 'Bob'; (35) |                                               |
|                                                | update table set age = 40 where name = 'Bob'; |
| select age from table where name = 'Bob'; (40) |                                               |
|                                                | commit;                                       |

Non-Repeatable Read

| NAME  | AGE |
|:------|:----|
| Bob   | 35  |

| TRANSACTION T1                                 | TRANSACTION T2                                |
|:-----------------------------------------------|:----------------------------------------------|
| select age from table where name = 'Bob'; (35) |                                               |
|                                                | update table set age = 40 where name = 'Bob'; |
|                                                | commit;                                       |
| select age from table where name = 'Bob'; (40) |                                               |


Phantom Read

| NAME  | AGE  |
|:------|:-----|
| Bob   | 35   |

| TRANSACTION T1                                 | TRANSACTION T2                         |
|:-----------------------------------------------|:---------------------------------------|
| select count(*) from table where age = 35; (1) |                                        |
|                                                | insert into table values ('jack', 35); |
|                                                | commit;                                |
| select count(*) from table where age = 35; (2) |                                        |

To prevent the following read issues, isolation levels are provided

| ISOLATION-LEVEL   | DIRTY-READ | NON-REPEATABLE-READ | PHANTOM-READ |
|:------------------|:-----------|:--------------------|:-------------|
| READ_UNCOMMITED   | YES        | YES                 | YES          |
| READ_COMMITED     | NO         | YES                 | YES          |
| READ_REPEATABLE   | NO         | NO                  | YES          |
| READ_SERIALIZABLE | NO         | NO                  | NO           |

### 6. Indexing

1. Clustered index - A special type of index that reorders the way records in the table are physically stored. Therefore, table can have only one clustered index. The leaf nodes of a clustered index contain the data pages. eg: primary key
2. Non-clustered index - A special type of index in which the logical order of the index does not match the physical stored order of the rows on disk. The leaf node of a non-clustered index does not consist of the data pages. Instead, the leaf nodes contain index rows. eg: unique constraints

| Clustered Index                         | Non-Clustered Index                         |
|:----------------------------------------|:--------------------------------------------|
| Faster                                  | Slower                                      |
| Requires less memory                    | Requires more memory                        |
| Index leaf node is the main data        | Index leaf node is pointer to  data         |
| Table can have only one clustered index | Table can have multiple non-clustered index |

1. Each new index will increase the time it takes to write new records.
2. The where clause should have columns which are indexed for the right index to be used.
3. like clause doesnt use index column because it's a match query.
4. hints - If you know that a query needs to be use a particular index you can provide hints, the db can choose to use it or not.

Will explain the plan
```
EXPLAIN SELECT * FROM table;
```

Will execute and explain the plan
```
EXPLAIN ANALYZE * FROM table;
```

[https://youtu.be/-qNSXK7s7_w](https://youtu.be/-qNSXK7s7_w)

### 7. Vertical Scaling vs Horizontal Scaling

![](scaling.png)

### 8. Datacenter

![](datacenter.png)

### 9. Distributed System

Things to consider while designing distributed system

1. Fault Tolerance & Retry - Always assume that things will fail and plan for it. Eg: Network failures, Disk failures
2. Circuit Breaker Pattern - Instead of throwing error page handle service down gracefully.
3. Service Discovery - All services register themselves.
4. Observability - System is actively monitored.
5. Fail-Over - Stand up server go live when primary servers dies.
6. Throughput - The number of requests the system can process.
7. Latency - Time taken to process the requests.
8. Rate Limit - Restrict overuse of services by single or many users.
9. Caching - Caching speeds up lookup however can bring in-consistency among caches.
10. Bulk head pattern - Failure in one system should not bring down the whole system.
11. Timeout - Ensure proper connection timeouts are set so that slow downstream service cant impact upstream service.
12. Fail-fast - Prefer to fail fast than deal with slow latency, as it can cascade the effect in upstream services.
13. Chaos Monkey - Randomly turn off systems to ensure system is fault-tolerant.

### 10. Caching

Caching improves performance your application, reduces latency, reduces load on the DB, reduces network cost, increases read throughput. 
However, it does come with cost/problems of its own like cache invalidation, stale data, high churn if TTL is set wrong, thundering herd etc.
In a distributed system a distributed cache (read-write) comes with problems of its own like consistency, node affinity etc. 
Hence don't look at cache as a silver bullet to solve all problems.

Different places to cache

1. Client side caching - When the client or browser can cache some data to avoid the external call.
2. Server side caching - Each server can cache some data locally.
3. Global/Distributed caching - A centralized server/service to cache data.
4. Proxy/Gateway side caching - Proxy or gateway servers cache some data so the request can be returned immediately without reachout to backend.

### 11. Types of Cache

1. Spatial cache
2. Temporal cache
3. Distributed cache

### 12. Cache Store

![](cache-store.png)

1. On-Heap Store - stores cache entries in Java heap memory
2. Off-Heap Store -  primary memory (RAM) to store cache entries, cache entries will be moved to the on-heap memory automatically before they can be used.
3. Disk Store - uses a hard disk to store cache entries. SSD type disk would perform better.
4. Clustered Store - stores cache entries on the remote server

### 13. Cache Eviction Policies

1. FIFO (First In First Out) - replaces first element that was added to the cache.
2. LIFO (Last In First Out) - replaces the last element that was added to the cache.
3. LRU (Least Recently Used) - replaces element that has not been used for long time.
4. MRU (Most Recently Used)- replaces most recently used elements.
5. LFU (Least Frequently Used) - replaces least frequently used elements based on count.
6. RR (Random Replacement) - replaces elements randomly.

### 14. Caching Strategy

1. Read-Cache-aside - Application queries the cache. If the data is found, it returns the data directly. If not it fetches the data from the SoR, stores it into the cache, and then returns.
2. Read-Through - Application queries the cache, cache service queries the SoR if not present and updates the cache and returns.
3. Write-Around - Application writes to db and to the cache.
4. Write-Behind / Write-Back - Application writes to cache. Cache is pushed to SoR after some delay periodically.
5. Write-through - Application writes to cache, cache service immediately writes to SoR.

![](cache-strategy.png)

### 15. High Availability Deployment

1. Active-Active - Two nodes of the service running in parallel, loadbalancer will route traffic to both.
2. Active-Passive - The primary and secondary service running in parallel, with primary serving all the requests. If primary fails loadbalancer will route traffic to secondary and designate it as primary.

### 16. CDN & Edge Servers

Content Delivery Network (CDN) reduce latency by keeping static content closer to the customer regions.
Always remember to version your static content like css, images etc to help CDN differentiate between versions.

1. Push CDN - Developer pushes the static content to CDN
2. Pull CDN - First request pulls the static content to the CDN

Edge Servers run compute operations closer to the customer region, eg: Streaming, Gaming etc. 

### 17. Message Broadcast

![](message-broadcast.png)

### 18. Kafka

Kafka is a distributed & fault-tolerant,high throughput, scalable stream processing & messaging system.

1. Kafka as publisher-subscriber messaging system.
2. Kafka as queue (point-point) messaging system.
3. Kafka as stream processing system that reacts to event in realtime.
4. Kafka as a store for data.

![](kafka-architecture.png)

![](kafka-partition.png)

Kafka provides high throughput because of the following

1. Kafka scales because it works on append only mode, sequential disk write is faster than random access file write
2. Kafka copies data from disk to network by ready with zero copy. OS buffer directly copies to NIC buffer.

[https://gitorko.github.io/post/spring-apache-kafka/](https://gitorko.github.io/post/spring-apache-kafka/)

[https://youtu.be/Cie5v59mrTg](https://youtu.be/Cie5v59mrTg)

[https://youtu.be/UNUz1-msbOM](https://youtu.be/UNUz1-msbOM)

### 19. Rabbit MQ

![](rabbit-mq.png)

[https://youtu.be/O1PgqUqZKTA](https://youtu.be/O1PgqUqZKTA)

### 20. Rabbit MQ vs Kafka

| RabbitMQ                             | Kafka                              |
|:-------------------------------------|:-----------------------------------|
| Consumed event deleted, Less storage | All events stored, More storage    |
| Queues are single threaded           | Can scale based on consumer groups |
| Complex with more brokers            | Scales with more brokers           |
| No events replay                     | Events can be read from any point  |
| Ordering guaranteed                  | Ordering per partition             |
| Push model                           | Push & Pull model                  |

[https://www.upsolver.com/blog/kafka-versus-rabbitmq-architecture-performance-use-case](https://www.upsolver.com/blog/kafka-versus-rabbitmq-architecture-performance-use-case)

[https://tanzu.vmware.com/developer/blog/understanding-the-differences-between-rabbitmq-vs-kafka/](https://tanzu.vmware.com/developer/blog/understanding-the-differences-between-rabbitmq-vs-kafka/)

### 21. Rabbit MQ Streams

[https://blog.rabbitmq.com/posts/2021/07/rabbitmq-streams-overview](https://blog.rabbitmq.com/posts/2021/07/rabbitmq-streams-overview)

### 22. JVM Memory & Garbage collectors

1. Minor GC
2. Major GC

![](jvm-memory.png)

### 23. Proxy vs Reverse-Proxy

![](proxy.png)

### 24. Load Balancer

1. Software based
2. Hardware based

Load balancer distributes traffic across multiple nodes ensuring high availability. 
Always create health check url that can determine if node is healthy or not, based on this the load balancer decides if the node is up or down.

1. L3 - IP Based
2. L4 - DNS Based
3. L7 - Application Based

Sticky sessions - Will assign the same user request to the same node in order to maintain the session state on the node. 
Ideally sticky session should be avoided, if the node goes down few users will experience outage. However in some cases sticky session will be easy to configure and setup.

### 25. Load Balancer Routing Algorithms

1. Round-robin - traffic distributed in round-robin fashion.
2. Weighted Round-robin - traffic distributed by weight, some servers may be able to process more load hence their weight is more compared to smaller configuration machines.
3. Least Connections - traffic is sent to server with the fewest current connections to clients.
4. Least Response Time - traffic is sent to server with the fastest response time.
5. Least Bandwidth -  traffic is sent to server with the least Mbps of traffic.
6. Hashing - traffic is sent to server based on a hash key. eg: client IP address hash, request URL hash.

### 26. NoSQL vs Relational DB

| NoSQL                                                          | RDBMS                                     |
|:---------------------------------------------------------------|:------------------------------------------|
| Non-Relational DB                                              | Relational DB                             |
| No predefined schema, handles un-structured data               | Require a schema, handles structured data |
| Can scale across machines                                      | Cant scale across machines easily         |
| BASE Principle of eventual consistency                         | ACID properties                           |
| Structure can be Key-Value pairs, Document, Graph, Wide column | Stricture is Table based                  |

### 27. CQRS - Command and Query Responsibility Segregation

Pattern that separates read and update operations for a data store.
Implementing CQRS in your application can maximize its performance, scalability, and security

![](cqrs.png)

### 28. HTTP1 vs HTTP1.1 vs HTTP2 vs HTTP3 Protocol

1. HTTP1 - one tcp connection per request
2. HTTP1.1 - one tcp connection per request, keep alive connection so connection is not closed immediately.
3. HTTP2 - one tcp connection for all requests. Multiplex all requests on one TCP. Server Push where the server proactively pushes css,js all on one TCP when the server requests the html file.
4. HTTP3 - Uses QUIC protocol (based on UDP). Eg: Mobile that is changing cell towers, UDP continues to stream data without a new TCP handshake with the new tower.

![](protocols-http.png)

[https://youtu.be/a-sBfyiXysI](https://youtu.be/a-sBfyiXysI)

### 29. HTTPS

Asymmetric encryption vs symmetric encryption

![](working-https.png)

### 30. Thundering Herd Problem

![](thundering-herd.png)

### 31. Tsunami Traffic

1. Scaling up services takes time, Keep few services on standby if you anticipate heavy traffic.
2. Configure auto-scaling based on key parameters.
3. Scale on concurrent requests & not on CPU or memory usage.
4. Design for scale down models as well along with scale up.
5. Identify breaking point for each system.
6. Plan for service denial via circuit breakers for new customers instead system wide outage for all customers.

### 32. Serverless / FAAS (Function As A Service)

1. Function is exposed as a service.
2. Cost optimal, pay for only what you use.

### 33. Bloom filter

Determines if given element is present in a set or not (member of set). In some cases it can give false positive, but will never give a false negative. 
More hash functions you use lesser the collisions, wider the bit array lesser the collisions.

1. To determine 'Member of set'
2. No false negative but can give false positive
3. Less memory used, entire bloom filter result can be sent over wire.
4. Probabilistic algorithm

![](bloom-filter.png)

Real world uses

1. Malicious url detection via bloom filter.
2. CDN cache url, cache page only if 2nd request.
3. Weak password detection.
4. Username already taken.

[https://youtu.be/Bay3X9PAX5k](https://youtu.be/Bay3X9PAX5k)
[https://youtu.be/V3pzxngeLqw](https://youtu.be/V3pzxngeLqw)

### 34. Count Min Sketch

Count frequency of event in streaming data, uses multiple hash to map frequency on to a matrix. Uses less space.
In some cases it can over count due to hash collision but never under-count the events, this is the probabilistic nature.

1. Count frequency of events, range query, total, percentile.
2. Uses less memory.
3. Probabilistic algorithm.

Every event is passed via multiple hash functions and respective matrix row/column updated. The frequency is determined by the minimum of all these counts. For more accuracy you can add more hash functions and wider column. 
In the example below hash generates numbers 0-6. Lesser hash functions will result in more collisions.

![](count-min-sketch.png)

[https://youtu.be/ibxXO-b14j4](https://youtu.be/ibxXO-b14j4)

### 35. BitMap

1. Bit Array, Uses less memory
2. Each bit holds value
3. Using AND / OR operation can merge many bitmaps

Eg: Daily site visitor count.

### 36. Contention

1. Avoid locks if you want to scale, as they cause contention around shared resources
2. Do pre-work instead of on-demand if it avoids contention. Eg: Issue 1 Billion tickets, instead of updating a row in DB with locks/syncronization, load a queue with all 1 Billion tickets and users can pick from queue.

### 37. Paxos

1. Consensus over distribute system
2. Leader election

[https://youtu.be/WX4gjowx45E](https://youtu.be/WX4gjowx45E)

[https://youtu.be/s8JqcZtvnsM](https://youtu.be/s8JqcZtvnsM)

### 38. CAP Theorem

![](cap-theorem.png)

[https://youtu.be/KmGy3sU6Xw8](https://youtu.be/KmGy3sU6Xw8)

### 39. ACID

1. Atomicity - All changes to data are performed as if they are a single operation
2. Consistency - Data is in a consistent state when a transaction starts and when it ends.
3. Isolation - The intermediate state of a transaction is not visible to other transactions.
4. Durability - Data persisted survives even if system restarted.

### 40. Database Scaling

1. Read scaling - Replication, All writes goto one db node, which gets replicated to all read node db. (eventual consistency)
2. Write scaling - Sharding

![](db-scaling.png)

### 41. Partition vs Sharding

1. Partitioning - Breaks up data into many smaller blocks within the same database server. Client need not be aware of partitions. 
   a. Horizontal partition - Based on key the data is split. eg: All records for 2021 get written to partition_2021, all 2022 records get written to partition_2022
   b. Vertical partition - Based on some column the data is split. eg: All the image blob of a profile are stored in a different table.
2. Sharding - Breaks up data into many smaller blocks in different database servers. Client must be aware of shards. Cant do transactions or joins across shards. If data distribution is not uniform then will have to re-balance shards.
   eg: All customer records A-H go to database server1, all records I-Z go to database server2.


When to Partition?
1. When the table is too big for even indexes to search. Partition bring in improvement in query performance.
2. When you need to purge old records as part of data management. Easier to drop partition than delete rows.
3. Bulk loads and data deletion can be done much faster, as these operations can be performed on individual partitions.

When to Shard?
1. To scale out horizontally.
2. When there are too many writes.
3. When data is transaction isolated, and you don't need to join across shards.
4. If data is uniformly distributed among shards then query load is also equally distributed.

Sharding on postgres using postgres_fdw extension.

```sql
CREATE TABLE customer (
 id BIGSERIAL NOT NULL,
 name VARCHAR(255) NOT NULL,
 city_id INT NOT NULL,
 created_on TIMESTAMP NOT NULL,
);

CREATE EXTENSION postgres_fdw;
GRANT USAGE ON FOREIGN DATA WRAPPER postgres_fdw to app_user;
CREATE SERVER shard02 FOREIGN DATA WRAPPER postgres_fdw
    OPTIONS (dbname 'postgres', host 'shard02', port '5432');
CREATE USER MAPPING for app_user SERVER shard02 OPTIONS (user 'app_username', password 'app_password');
    
CREATE FOREIGN TABLE customer_2021 PARTITION OF customer
    FOR VALUES FROM ('2021-01-01') TO ('2021-12-31')
    SERVER remoteserver01;
```

### 42. Partition Criteria

1. Hash Based
2. List Based
3. Range Based
4. Composite - multiple partitions under a partition

Hash Partition

```sql
CREATE TABLE customer (
  id BIGSERIAL NOT NULL,
  name VARCHAR(255) NOT NULL,
  city_id INT NOT NULL,
  created_on TIMESTAMP NOT NULL,
) PARTITION BY HASH (id);
CREATE TABLE customer_even PARTITION OF customer FOR VALUES WITH (MODULUS 2,REMAINDER 0);
CREATE TABLE customer_odd PARTITION OF customer FOR VALUES WITH (MODULUS 2,REMAINDER 0);
```

Range Partition

```sql
CREATE TABLE customer (
  id BIGSERIAL NOT NULL,
  name VARCHAR(255) NOT NULL,
  city_id INT NOT NULL,
  created_on TIMESTAMP NOT NULL,
) PARTITION BY RANGE (created_on);
CREATE TABLE customer_2021 PARTITION OF customer FOR VALUES FROM ('2021-01-01') TO ('2021-12-31');
CREATE TABLE customer_2022 PARTITION OF customer FOR VALUES FROM ('2022-01-01') TO ('2022-12-31');
```

List Partition
```sql
CREATE TABLE customer (
  id BIGSERIAL NOT NULL,
  name VARCHAR(255) NOT NULL,
  city_id INT NOT NULL,
  created_on TIMESTAMP NOT NULL,
) PARTITION BY LIST (EXTRACT(YEAR FROM created_on));
CREATE TABLE customer_2021 PARTITION OF customer FOR VALUES IN('2021');
CREATE TABLE customer_2022 PARTITION OF customer FOR VALUES IN('2022');
```

### 43. Bulkhead pattern

If one microservice is slow it can end up blocking threads and there by affecting all other microservices. Solution is to have dedicated thread pool for each client.
It isolates dependencies, so that problem in one dependency doesn't affect others. A counter can also be used with max limits instead of creating different thread pool.
Fail-Fast is preferred over slow service.

![](bulk-head.png)

[https://youtu.be/R2FT5edyKOg](https://youtu.be/R2FT5edyKOg)

### 44. Circuit Breaker

If a service is down, we want to avoid continuously making calls to the service, till it gets time to recover. We will then send a default response such a case.
If the number of request failures are above a threshold then we decide to return a default response.
After a certain period we will allow certain request to hit the service and if the response is good, we will allow all the traffic.

States

1. Open - No traffic is sent.
2. Closed - All traffic is sent.
3. Half-Open - After timeout only few calls are allowed.

![](circuit-breaker.png)

[https://youtu.be/ADHcBxEXvFA](https://youtu.be/ADHcBxEXvFA)

### 45. Consistent Hashing

Nodes keep dying in a distributed system. To scale new nodes can be added as well. Consistent hashing lets you distribute traffic among the nodes uniformly.

Why not use round-robin to distribute traffic?
Services often cache some data, so it makes for a better design if the same client request is sent to the server which has all the data already cached. If you send the same client request randomly to random servers each time then cache is not utilized.

Consistent hashing also prevents DOS attacks to some extent. If a spam client send random requests and round robin distributes it across all nodes then the outage is large scale. However with consitent hashing only certain node will be impacted.

If you just hash the request and map it to a server then if the node count changes all the requests will be impacted and will move to different servers. Hence in consistent hashing we hash both the request and the servers to a hash space and link them in a hash ring.
With consistent hashing adding a new servers affects only few requests.
The distribution of servers in a hash ring may not be uniform hence you can use virtual servers. With more virtual servers the distribution is more balanced.
Eg: if there are 60K user requests and there are 6 servers each server can distribute and handle 10K. Do note that if one node goes down then all the requests flood the next node causing it to go down thus causing a full outage. Virtual servers will avoid this to some extent.

![](consistent-hashing.png)

[https://youtu.be/UF9Iqmg94tk](https://youtu.be/UF9Iqmg94tk)

### 46. Rate limit

1. Token Bucket - Burst - Fixed token are added to bucket, bucket is always kept in full state. Can lead to burst of traffic.
2. Token Bucket - Sustain - Constant token are added to bucket only if previous token are consumed. Smooth traffic.
3. Leaky Bucket - Bucket size if fixed, if bucket full request are rejected, a processor de-queue bucket at fixed rate.
4. Fixed Window - For the time period maintain a key,value pair. If counter is greater than rate limit reject. Leads to burst traffic around edges of time period.
5. Sliding Log - Go over all previous nodes upto the time interval, in the link list and check rate limit exceeded, if yes then reject.
6. Sliding Window Counter - Go over all previous nodes upto the time interval, in the link list and check if rate limit exceeded, if yes then reject. Instead of storing each request timestamp previous node stores the count.

![](rate-limit.png)

![](rate-limit-distributed.png)

[https://youtu.be/9CIjoWPwAhU](https://youtu.be/9CIjoWPwAhU)

[https://youtu.be/FU4WlwfS3G0](https://youtu.be/FU4WlwfS3G0)

### 47. Push vs Pull

1. RabbitMQ is push based, Kafka is pull based
2. Push is expensive & real-time
3. Pull is cheap but not real-time

### 48. NIO

1. Non-Blocking IO helps systems scale with fewer resources.
2. The complete source to destination flow has to be non-blocking.

### 49. Multi-Tenancy

1. Multiple customers share same resource but customer are not aware of each other and instances are isolated.
2. Kubernetes namespaces

### 50. Authorization vs Authentication

1. Authentication - Is the user allowed to use the system?
2. Authorization - Does the user have the right role to execute that operation?

### 51. Service Mesh

1. Service Discovery
2. Load Balancing
3. Fault Tolerance
4. Distributed Tracing
5. Telemetry
6. Security

Service-to-service communication is essential in a distributed application but routing this communication, both within and across application clusters, becomes increasingly complex as the number of services grows. Service mesh enables managed, observable, and secure communication between individual services. It works with a service discovery protocol to detect services. Istio and envoy are some of the most commonly used service mesh technologies.

### 52. Deployment Strategy

Ensure that database schema works with both new version and old version of the service.
Provide health check url to determine if node is healthy.

1. Rolling - Services are upgraded one after the other.
2. Blue Green - Few services are upgraded and test teams validate and signoff before all services are upgraded.
3. Canary - Few services are upgraded and part of the traffic hits these new instances.

![](deployment-model.png)

### 53. GeoHashing & Quadtree

Geohashing is a geocoding method used to encode geographic coordinates such as latitude and longitude into short alphanumeric strings.
Coordinates lat 40.730610, long -73.935242. can be represented in geohash as `af3bdmcef`.
By comparing strings we can tell if the 2 locations are closer to each other, depending on how many chars in string match.

eg: Geohashes `af3bdmcef` and `af3bdmcfg` are spatially closer as they share the prefix `af3bdm`.

1. Easier to store in DB.
2. Easier to share in URL.
3. Easier to find nearest neighbour based on string match.

![](geo-hashing.png)

A quadtree is an in-memory tree data structure that is commonly used to partition a two-dimensional space by recursively subdividing it into four quadrants (grids) until the contents of the grids meet certain criteria.
Internal node has exactly four children, only the leaf nodes store the actual value. Quadtrees enable us to search points within a two-dimensional range.

Eg: Identify all restaurants/cabs in the 1.5 miles/km range from given point. If there are no restaurants/cabs in the grid/node then add neighbouring grids/nodes.

![](quad-tree.png)

### 54. Optimistic vs Pessimistic Locking

Locking is required to prevent the row from being updated by 2 threads concurrently there by corrupting the data.

1. Pessimistic Locking - The lock is now applied by the database at row level or table level. If the lock is a WRITE lock it prevents other threads from modifying the data. eg: SELECT * from TABLE where id = 1 for update;
2. Optimistic Locking - A version field is introduced to the database table, The JPA ensures that version check is done before saving data, if the version has changed the update will throw Error. Scalability is high with this approach.

### 55. Event sourcing

Instead of storing the update to an object/record, change the db to append only. Every change to the object/record is stored as a new entry in append fashion.

Eg: A customer record, each time address of customer changes instead of updating existing column, just insert new row with the new address. 
A materialized view can be generated from this data to get the latest customer record. Combining all the records gives latest customer record.

![](event-sourcing.png)

1. Updates can come from multiple sources, there is no contention to update.
2. Consistency for transactional data based on the time the event was processed.
3. Maintain full audit trails and history.
4. Slower to generate the materialized view.

### 56. Attack surfaces

To avoid security issues, the objective of all systems must be to reduce the number of attack surfaces. More the components in your system, more the attack surfaces that need to be hardened.

1. Network packet spoofing / eavesdropping - Someone on the same network can look at http packets using tools like wireshark, http packets are un-encrypted. Use https to prevent this attack
2. Man-in-the-middle attack - Someone pretending to be the actual client, Use SSL authentication with symmetric encryption.
3. Denial-Of-Service - Someone can overload your server and keep it busy so valid requests won't be processed. use rate limiting, IP blacklisting.
4. Bot attack - Millions of bots can be made to looks like real traffic is hitting your service. Use re-captcha to identify real users.
5. Storing CVV, passwords in DB - Avoid storing plain text passwords in DB. Always use salt (piece of random data added to a password before it is hashed and stored)
6. Reading Passwords - Avoid assigning passwords to Strings, instead assign them to char array. String use string pool in java so the password are not garbage collected immediately and may show up in heap dumps.
7. Open ports - Enable security groups to open ports that are required. The process is called hardening of servers.
8. Token expiry - Always set short expiry (TTL) for tokens, if compromised then the token will expire soon.
9. Roles - Always provide only needed roles to users, so that even if password is compromised permissions restrict them from doing more damage.
10. DMZ - Demilitarized zone, restrict backend servers from having direct access to internet. If backend servers need internet configure a forward proxy.
11. SSH Tunneling - SSH to a primary server and then open a tunnel to the actual server. 
12. Auditing - Always ensure proper auditing and logging is available to trace any breaches.
13. Backup & Checkpoint - Always ensure proper backups are available in case data needs reconciliation. Checkpoint run at short interval capturing the snapshot of the current system.

### 57. Kubernetes

[https://gitorko.github.io/post/kubernetes-basics/](https://gitorko.github.io/post/kubernetes-basics/)

### 58. Indexing - Btree, B+tree, BitMap

1. BitMap index - A binary array to represent value, Uses less memory.
2. Btree - Creates a balanced tree on insert.
3. B+tree - Similar to btree but values are present only in the node. Improves range queries.

Btree (Max Degree 3)

![](btree.png)

B+tree (Max Degree 3)

![](bplustree.png)

[https://youtu.be/UzHl2VzyZS4](https://youtu.be/UzHl2VzyZS4)
[https://youtu.be/5-JYVeM3IQg](https://youtu.be/5-JYVeM3IQg)

[https://www.cs.usfca.edu/~galles/visualization/BTree.html](https://www.cs.usfca.edu/~galles/visualization/BTree.html)
[https://www.cs.usfca.edu/~galles/visualization/BPlusTree.html](https://www.cs.usfca.edu/~galles/visualization/BPlusTree.html)

### 59. Data Race & Race conditions

Data Race - Multiple threads access shared variable at same time without synchronization & at least one thread is writing, can cause corruption. Eg: Addition to long/double which are 64 bits.

Race conditions - Multiple threads access shared variable, value of variable depends on execution order of threads.  Atomic looking operations are not done atomically.

Race conditions can be of 2 types

1. Check & Update - When two threads check if value present in map and put if absent.To prevent use locks or putIfAbsent atomic operations. 
2. Read & Update - When two threads read a value and increment it. Use locks or atomic variables.

[https://youtu.be/KGnXr62bgHM](https://youtu.be/KGnXr62bgHM)

### Others

* HDFS - Distributed filesystem
* Zookeeper leader election quorum
* Gateway
* Distributed tracing - Zipkin
* Observability - wavefront, prometheus, nagios
* Hadoop - Map Reduce
* CAS - compare and swap
* Client side load balancing
* GitOps
* Telemetry
* Pub Sub vs Queue
* Block chain - distributed ledger
* Concurrent HashMap Internals
* Race conditions
* Disaster recovery
* Auto scaling
* Batch vs Stream data processing vs Micro Batch
* Star vs Snow flake schema
* Inverted indexing
* Time Series Database
* Hyperlog
* Elasticsearch
* OAuth 2.0
* Java Fibers - Project Loom

## Scenarios

### 1. Design a shopping application where users can browse products and buy them.

* If the products are rendered on a web page for each request, then the system won't scale.
* Browsing products is more frequent than buying something.
* Generate a static website and upload to CDN, only the buy rest api calls hit the backend server.
* Home pages or landing pages which are frequently hit perform better if they are static sites and on the CDN.
* Even for user tailored home pages like Netflix, Hotstar etc, generate static sites per user and avoid actual backend calls as much as possible.

![](shopping-application.png)

* There are 2 services hence to we use a queue to rollback the transaction in case of any failures. i.e. Saga pattern
* We use a DB to store the state in case the service go down they can recover from that point.

{{% notice tip "Tip" %}}
If you can design a system where the calls never have to hit your backend service it improves the design. Eg: CDN, Edge Server, Cache etc.
Look at client side caching as well if it means avoiding that backend call.
{{% /notice %}}

### 2. Design a URL shortener service (Tiny URL)

Users will provide a long url, your service needs to return a short url. If the users give you the same short url you need to return the actual url.

* If you generate a short url with UUID there can be collision if the key is same. More **collisions** more time is spent in returning a response degrading your service. The system will not scale.
* If the pre-created short url are stored in a RDBMS database there is contention at the db when all the threads ask for the next free key.
* Ensure that pre-created short url are not sequential so that someone should not guess what the next key can be simply by incrementing one character.

![](url-shortner.png)

* Given a key find the URL - We will use and RDMBS database for this. RDBMS handles the fetch very well and scales well. We dont use a NO-SQL db as we want consistency (CAP).
* We will use 2 table, first one where primary key is the short url and value is the actual url. The second table where primary key is the actual url and the value is the short url.
* A Generate key service will populate the queue with the existing short-url generated. Generate key service will ensure that duplicate keys are not loaded by generating short urls in range eg: A-F, G-N ranges.
* A consistent hashing service will handle the situation where we add more queues to the group.
* The queue represented here is redis poll queue and not a listener based or a pub-sub queue.
* The put operation first fetches a key from the queue, since there are multiple queues there is no contention to get a new key. It then writes the key & value to the RDBMS.
* If there is heavy writes at RDBMS then sharding can be done. The service needs to be aware of the shards to write to and read from.
* Nodes go down often, so if the queues die then there can be unused keys that are forever lost. We use a reconciliation task that runs nightly to recover any lost keys.
* The service doesn't need to be aware of ranges hence we dont need any Zookeeper or consensus manager. If the short url has to be generated on fly then you can use DB to know the ranges each nodes is handling, overhead of zookeeper doesn't justify the benefits.
* You can use a LRU cache to improve the get responses. Use one of the Caching Strategy discussed above.

{{% notice tip "Tip" %}}
Avoid collisions, on a new environment there will be less collisions but as your data grows collisions will increase. 
{{% /notice %}}

{{% notice tip "Tip" %}}
Avoid contention for resources, contentions grow exponentially as system scales. The simple act of asking the DB for the next free record among a set, incrementing a particular row value are examples where contention can occur.
{{% /notice %}}

{{% notice tip "Tip" %}}
Don't hesitate to recommend RDBMS for high scale systems. Given a key find the record, RDBMS does this job very well. Remember Youtube uses RDBMS.
{{% /notice %}}

### 3. Design a Youtube / Facebook like counter service

Users can like a post/video and submit their likes, the service needs to count how many likes a post/video has.

* A single counter that needs to be updated by many threads always creates contention.
* Addition operation needs to be atomic making it difficult to scale.
* If you treat the counter as a row in the DB and use optimistic locking with retry logic to increment with exponential backoff you avoid locking the resource but there are multiple attempts to update the counter which causes scale issues. Hence DB is out of picture.
* You can read more about 'Dynamic Striping' & Long Adder & Long Accumulator to get an idea how java does addition operation on scale. However this is restricted to a single instance.
* If you consider each like counter as a new row you avoid contention of an update but more time is spent in summing up the total by counting all rows.

![](like-service.png)

* Redis provides **atomic** operations of increment. We dont want to keep a single video like counter on one node as it can overload it if there are more likes for that video compared to others.
* By using **Round Robin** we can scale our service as by adding more redis nodes.
* We use a **Pub-Sub** event model to let the count aggregator service to sum the counts across all redis nodes and save that to a DB.
* The get count will always read the DB for latest count. There will be a slight delay from the time we submit the like till we see the count, however this is **eventual consistency**.
* The event queue payload can carry information about nodes that got updated, this way the aggregator service need not iterate over all redis nodes.

{{% notice tip "Tip" %}}
Avoid updating DB rows in most cases, updates don't scale. Always prefer using inserts over updates.
{{% /notice %}}

{{% notice tip "Tip" %}}
To prevent race conditions optimistic or pessimistic locking need to be used and they dont scale. Use redis for atomic increment & decrement as they guarantee atomicity.
{{% /notice %}}

### 4. Design an Advertisement Impression Service tied to a budget

For a give budget, ads of a particular type are served. Once the budget is exhausted the ads should not be served. 
For the type shoes, Nike has a budget of 1000$ and Adidas has a budget of 500$. When a website wants to display an ad it calls your service which randomly returns an ad, ensuring that the budget is not exceeded.
If each ad impression costs 1$ then you can do 1000 Ad impressions of Nike and 500 impression of Adidas.

* It looks similar to the like counter service, where we can (atomic) decrement the budget based on the number of Ads being served. An incoming request randomly picks an Ad and decrements the budget for that Ad till it reaches 0. However such a design will still run into contention when scaled.
* The contention occurs when we want to decrement the budget, since we cant distribute the budget value across multiple nodes, the decrement operation still needs to happen on one node and in atomic fashion.
* Assume there is only 1 Nike brand with a budget of 1M. Now when there is huge load since there is only 1 brand and the budget needs to be decremented as an atomic operation, even though redis can do atomic decrement operations, it will still slow down the system since all the threads are waiting to decrement the single budget entity.

![](ad-impression.png)

* A token seeder pre-populates dedicated queues with a single token. Based on the budget, an equal number of tokens are populated. Nike Queue will have 1000 token, Adidas Queue will have 500 tokens.
* When the request comes in for a shoe type Ad. A random queue is picked and a token dequeued. Based on that token the associated ad is served. 
* Once the Queue is out of tokens no Ads are served for that brand.
* The Ad fetcher service needs to be aware of which queue to deque, it needs to be aware of how many queues are associated with the given brand.
* If a queue goes down the token seeder can identify and recreate a new queue based on the transactional log that is held by each service to identify how many tokens were already served.
* If there is a new brand that wants to join, just create the tokens and seed a queue and add it to the group. The next round-robin should pick it up.

{{% notice tip "Tip" %}}
Instead of incrementing/decrementing a counter, check if it's possible to create tokens ahead of time. With a bunch of tokens in a queue/bucket it's easier to scale than trying to update a single counter in atomic fashion.
{{% /notice %}}

### 4. Design a Code Deployment System

Build the code when someone commits code to a branch and deploy it to a machine.

* Builds can take long time to complete hence split the task into 2, if deploy fails we don't want to build again.
* Writing the records to DB would take more time compared to pushing to queue and polling the DB would need retry mechanism without wasting cpu cycles, hence using RabbitMQ is a better fit.
* Builds can take time, so we dont want the manager service constantly polling workers. Once the worker completes it will push an event that will be consumed by manager service to continue the deployment flow.
* If workers die during the build then heartbeat will not be updated and a scheduler can restart the job. If the build nodes make a direct connection for heart beat this can overwhelm the manager service as there will be many worker nodes.

![](code-deployment.png)

{{% notice tip "Tip" %}}
Split the tasks into smaller sub-tasks so that they can be restarted in case of failure.
{{% /notice %}}

{{% notice tip "Tip" %}}
Asynchronous jobs can be queued and processed.
{{% /notice %}}

### 5. Design a large scale file de-duplication service

You will receive a number of files (customer records) in a folder once a day, the file sizes range from 10GB-50GB that need to be de-duplicated based on few columns eg: Name & phone number column.

* Processing a large file takes time. So chunking the file into manageable sizes helps distribute the task, and restart if some tasks fail.
* Avoid in-memory processing like Sets/Maps which can easily run out of memory.
* You can use a database with unique constraints, but this is write intensive task hence won't scale.
* Since the files arrive once a day, this is more batch oriented and not streaming task.
* Use a Bloom Filter a probabilistic data structure. This is used to test whether an element is a member of a set. There can be False-positive matches but no false negatives. Refer above section for overview of Bloom Filter.
* Pick a big bit array & many hash functions to avoid collision this will avoid false positives as much as possible.
* Bloom filter bit array resides in memory hence ensure that the file is processed by the same service. If the bit array needs to be shared, use redis in-memory BITFIELD
* If false positive can not be avoided despite the large hash range, we can rely upon db unique constraints check as the 2nd level of safety. 

![](file-dedupe.png)


{{% notice tip "Tip" %}}
Smaller tasks take less time, can be restarted, can be distributed. Always check if the input data can be chunked.
{{% /notice %}}

{{% notice tip "Tip" %}}
Use Bloom Filter to test if an element is a member of a set.
{{% /notice %}}

### 6. Design a flash sale system

[https://gitorko.github.io/flash-sale-system/](https://gitorko.github.io/post/flash-sale-system/)

### 7. Design a chat server

[https://gitorko.github.io/chat-server/](https://gitorko.github.io/post/chat-server/)

### 8. Design a Voting service

[https://gitorko.github.io/voting-system/](https://gitorko.github.io/post/voting-system/)

### 9. Design a Stock Exchange (Price Time Priority Algorithm)

[https://gitorko.github.io/stock-exchange/](https://gitorko.github.io/post/stock-exchange/)

### 10. Design a ticket booking system

[https://gitorko.github.io/ticket-booking-system/](https://gitorko.github.io/post/ticket-booking-system/)

## Youtube Channels

[ByteByteGo](https://www.youtube.com/c/ByteByteGo)

[Hussein Nasser](https://www.youtube.com/c/HusseinNasser-software-engineering)

[DefogTech](https://www.youtube.com/c/DefogTech)

[TechDummiesNarendraL](https://www.youtube.com/c/TechDummiesNarendraL)

## References

[https://github.com/resumejob/system-design-algorithms](https://github.com/resumejob/system-design-algorithms)

