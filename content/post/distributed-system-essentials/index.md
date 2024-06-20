---
title: 'Distributed System Essentials'
description: 'Distributed System Essentials'
summary: 'Distributed System Essentials'
date: '2024-06-20'
aliases: [/points-of-failure/, /distributed-system-essentials/]
author: 'Arjun Surendra'
categories: [Distributed-System]
tags: [fail-fast, resilience4j, kubernetes, spring, postgres]
toc: true
---

We will look at the different points at which an application can fail in a distributed system and how to address failures.
We will deliberately fail the application at these points to determine what the error looks like and how to handle it. To know how to build a good distributed system you need to understand where it can fail.

Github: [https://github.com/gitorko/project57](https://github.com/gitorko/project57)

## Distributed System

![](generic-system.png)

### Blocking calls

{{% notice note "Problem" %}}
Your service is not responding as there are some requests that are taking very long to complete. They are waiting on IO operations. What do you do?
{{% /notice %}}

```bash
curl --location 'http://localhost:8080/api/job1/60'
```
Determine if compute intensive or IO intensive task and delegate the execution to a thread pool so that the core tomcat threads are free to serve requests. The default tomcat threads are 200 and any blocking that happens will affect the whole service.

There 2 types of protocol a tomcat server can be configured for

1. BIO (Blocking IO) - In the case of BIO the threads are not free till the response is sent back. (one thread per connection)
2. NIO (Non-Blocking IO) - In the case of NIO the threads are free to serve other requests while the incoming request is waiting for IO to complete. (many more connections than threads)

Either use JDK21 virtual threads or a framework like reactor which supports NIO (non-blocking IO)

![](img06.png)

```bash
curl --location 'http://localhost:8080/api/job2/60'
```

![](img07.png)

### Denial-of-Service (DOS) Attacks

{{% notice note "Problem" %}}
Your server is receiving a lot of bad TCP connections. A bad downstream client is making bad tcp connections that doesn't do anything, valid users are getting **Denial-of-Service**. What do you do?
{{% /notice %}}

Create 10 telnet connections that connect to the tomcat server and then invoke the rest api to getTime which will not return anything as it will wait till the TCP connection is free.

```bash
for ((i=1;i<=10;i++));
do
  echo $i
  telnet 127.0.0.1 8080 &
done
```

```bash
curl --location 'http://localhost:8080/api/time'
```

The connection timeout means - If the client is not sending data after establishing the TCP handshake for 'N' seconds then close the connection. The default timeout is 2 minutes

```bash
server.tomcat.connection-timeout=500
```

{{% notice warning "Note" %}}
Many developers will assume that this connection timeout actually closes the connection when a long running task takes more than 'N' seconds. This is not true.
It only closes connection if the client doesn't send anything for 'N' seconds.
{{% /notice %}}

### Time Limiter

{{% notice note "Problem" %}}
A new team member has updated an API and introduced a bug and the function is very slow or never returns a response. System users are complaining of a slow system?
{{% /notice %}}

Always prefer **fail-fast** instead of a slow system **fail-later**. By failing fast the downstream consumers of your service can use circuit breaker pattern to handle the outages gracefully instead of dealing with a slow api.

If a function takes too long to complete it will block the tomcat thread which will further degrade the system performance. Use Resilience4j `@TimeLimiter` to explicitly timeout long running jobs, this way runaway functions cant impact your entire system.

```bash
curl --location 'http://localhost:8080/api/job3/10'
```

You will see the error related to timeout

```
java.util.concurrent.TimeoutException: TimeLimiter 'project57-tl' recorded a timeout exception.
	at io.github.resilience4j.timelimiter.TimeLimiter.createdTimeoutExceptionWithName(TimeLimiter.java:225) ~[resilience4j-timelimiter-2.2.0.jar:2.2.0]
	at io.github.resilience4j.timelimiter.internal.TimeLimiterImpl$Timeout.lambda$of$0(TimeLimiterImpl.java:185) ~[resilience4j-timelimiter-2.2.0.jar:2.2.0]
	at java.base/java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:572) ~[na:na]
	at java.base/java.util.concurrent.FutureTask.run(FutureTask.java:317) ~[na:na]
	at java.base/java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:304) ~[na:na]
	at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1144) ~[na:na]
	at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:642) ~[na:na]
	at java.base/java.lang.Thread.run(Thread.java:1583) ~[na:na]
```

Spring also provides `spring.mvc.async.request-timeout` that you can explore to accomplish the same.

{{% notice info "Note" %}}
Always assume the functions/api will take forever and may never complete, design system accordingly.
{{% /notice %}}

### Request Thread Pool & Connections

{{% notice note "Problem" %}}
Users are reporting slow connection / timeout when connecting to your server? How many concurrent requests can your server handle?
{{% /notice %}}

The number of tomcat threads determine how many thread can handle the incoming requests. By default, this number is 200.

```bash
# Applies for BIO
server.tomcat.threads.max=200
```

Max number of connections the server can accept and process, for BIO (Blocking IO) tomcat the `server.tomcat.threads.max` is equal to `server.tomcat.max-connections`
You cant have more connections than the threads.

For NIO tomcat, the number of threads can be less and the max-connections can be more. Since the threads not blocked while waiting for IO to complete then can open up more connections and server other requests.

```bash
# Applies only for NIO
server.tomcat.threads.max=10
server.tomcat.max-connections=1000
```

{{% notice info "Note" %}}
Throughput - The number of tomcat threads and the server hardware determine how many requests can be served in a given time interval.
If you have 200 threads (BIO) and all request response on average take 1 second to complete then your server can handle 200 requests per second. When there is IO involved and context switching takes place throughput calculation becomes tricky.
{{% /notice %}}

### Keep-Alive

{{% notice note "Problem" %}}
Network admin calls you to tell that many TCP connections are being created to the same clients. What do you do?
{{% /notice %}}

TCP connections take time to be established, `keep-alive` keeps the connection alive for some more time incase the client want to send more data again in the new future. 

1. `max-keep-alive-requests` - Max number of HTTP requests that can be pipelined before connection is closed.
2. `keep-alive-timeout` - Keeps the TCP connection for sometime to avoid doing a handshake again if request from same client is sent.

```bash
server.tomcat.max-keep-alive-requests = 100
server.tomcat.keep-alive-timeout =  10
```

### Rest Client Connection Timeout

{{% notice note "Problem" %}}
You are invoking rest calls to an external service which has degraded and has become very slow there by causing your service to slow down. What do you do?
{{% /notice %}}

If the server makes external calls ensure to set the read and connection timeout on the rest client.
If you dont set this then your server which is a client will wait forever to get the response.

```bash
# If unable to connect the external server then give up after 5 seconds.
setConnectTimeout(5_000);
# If unable to read data from external api call then give up after 5 seconds.
setReadTimeout(5_000);
```

You will see below error when timeouts are set

```
2024-06-21T16:01:06.880+05:30 ERROR 25437 --- [nio-8080-exec-5] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed: org.springframework.web.client.ResourceAccessException: I/O error on GET request for "http://jsonplaceholder.typicode.com/users/1": Read timed out] with root cause
java.net.SocketTimeoutException: Read timed out
	at java.base/sun.nio.ch.NioSocketImpl.timedRead(NioSocketImpl.java:278) ~[na:na]
	at java.base/sun.nio.ch.NioSocketImpl.implRead(NioSocketImpl.java:304) ~[na:na]
	at java.base/sun.nio.ch.NioSocketImpl.read(NioSocketImpl.java:346) ~[na:na]
```

{{% notice info "Note" %}}
Always assume that all external API calls never return and design accordingly.
{{% /notice %}}

### Database Connection Pool

{{% notice note "Problem" %}}
Users are reporting slowness in api that fetch relatively small data from database. What do you do?
{{% /notice %}}

Spring boot provides Hikari connection pool by default. If there are run away SQL connections then service can quickly run out of connection in the pool and slow down the entire system.

We define the max pool size for connection

```bash
spring.hikari.maximumPoolSize: 5
```

By setting the connectionTimeout we ensure that when the connection pool is full then we timeout after 1 second instead of waiting forever to get a new connection.

```bash
spring.hikari.connectionTimeout=1000
```

Fail-Fast is always preferred than slowing down the entire service.

```
Caused by: org.hibernate.exception.JDBCConnectionException: Unable to acquire JDBC Connection [HikariPool-1 - Connection is not available, request timed out after 1001ms (total=5, active=5, idle=0, waiting=0)] [n/a]
	at org.hibernate.exception.internal.SQLExceptionTypeDelegate.convert(SQLExceptionTypeDelegate.java:51) ~[hibernate-core-6.5.2.Final.jar:6.5.2.Final]
	at org.hibernate.exception.internal.StandardSQLExceptionConverter.convert(StandardSQLExceptionConverter.java:58) ~[hibernate-core-6.5.2.Final.jar:6.5.2.Final]
	at org.hibernate.engine.jdbc.spi.SqlExceptionHelper.convert(SqlExceptionHelper.java:108) ~[hibernate-core-6.5.2.Final.jar:6.5.2.Final]
	at org.hibernate.engine.jdbc.spi.SqlExceptionHelper.convert(SqlExceptionHelper.java:94) ~[hibernate-core-6.5.2.Final.jar:6.5.2.Final]
	at org.hibernate.resource.jdbc.internal.LogicalConnectionManagedImpl.acquireConnectionIfNeeded(LogicalConnectionManagedImpl.java:116) ~[hibernate-core-6.5.2.Final.jar:6.5.2.Final]
	at org.hibernate.resource.jdbc.internal.LogicalConnectionManagedImpl.getPhysicalConnection(LogicalConnectionManagedImpl.java:143) ~[hibernate-core-6.5.2.Final.jar:6.5.2.Final]
	at org.hibernate.resource.jdbc.internal.LogicalConnectionManagedImpl.getConnectionForTransactionManagement(LogicalConnectionManagedImpl.java:273) ~[hibernate-core-6.5.2.Final.jar:6.5.2.Final]
	at org.hibernate.resource.jdbc.internal.LogicalConnectionManagedImpl.begin(LogicalConnectionManagedImpl.java:281) ~[hibernate-core-6.5.2.Final.jar:6.5.2.Final]
	at org.hibernate.resource.transaction.backend.jdbc.internal.JdbcResourceLocalTransactionCoordinatorImpl$TransactionDriverControlImpl.begin(JdbcResourceLocalTransactionCoordinatorImpl.java:232) ~[hibernate-core-6.5.2.Final.jar:6.5.2.Final]
	at org.hibernate.engine.transaction.internal.TransactionImpl.begin(TransactionImpl.java:83) ~[hibernate-core-6.5.2.Final.jar:6.5.2.Final]
	at org.springframework.orm.jpa.vendor.HibernateJpaDialect.beginTransaction(HibernateJpaDialect.java:176) ~[spring-orm-6.1.8.jar:6.1.8]
	at org.springframework.orm.jpa.JpaTransactionManager.doBegin(JpaTransactionManager.java:420) ~[spring-orm-6.1.8.jar:6.1.8]
	... 12 common frames omitted
Caused by: java.sql.SQLTransientConnectionException: HikariPool-1 - Connection is not available, request timed out after 1001ms (total=5, active=5, idle=0, waiting=0)
	at com.zaxxer.hikari.pool.HikariPool.createTimeoutException(HikariPool.java:686) ~[HikariCP-5.1.0.jar:na]
	at com.zaxxer.hikari.pool.HikariPool.getConnection(HikariPool.java:179) ~[HikariCP-5.1.0.jar:na]
	at com.zaxxer.hikari.pool.HikariPool.getConnection(HikariPool.java:144) ~[HikariCP-5.1.0.jar:na]
```

{{% notice info "Note" %}}
Always assume that you will run out of database connections due to a run away or storm of requests and design accordingly.
{{% /notice %}}

### Slow Query

{{% notice note "Problem" %}}
Users are reporting slowness in a db fetch api that fetches data from multiple tables via join. Your DBA also confirms that query is too slow. What do you do?
{{% /notice %}}

Slow queries often slow down the entire system. 
To test this we explicitly slow down a query with pg_sleep function.

We set timeout on the transaction `@Transactional(timeout = 5)` to ensure that slow query doesn't impact the entire system, after 5 seconds if the query doesnt return result an exception is thrown.

Fail-Fast is always preferred than slowing down the entire service.

```
2024-06-21T16:24:08.130+05:30  WARN 27713 --- [nio-8080-exec-2] o.h.engine.jdbc.spi.SqlExceptionHelper   : SQL Error: 0, SQLState: 57014
2024-06-21T16:24:08.130+05:30 ERROR 27713 --- [nio-8080-exec-2] o.h.engine.jdbc.spi.SqlExceptionHelper   : ERROR: canceling statement due to user request
2024-06-21T16:24:08.138+05:30 ERROR 27713 --- [nio-8080-exec-2] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed: org.springframework.dao.QueryTimeoutException: JDBC exception executing SQL [select count(*), pg_sleep(?) IS NULL from customer] [ERROR: canceling statement due to user request] [n/a]; SQL [n/a]] with root cause

org.postgresql.util.PSQLException: ERROR: canceling statement due to user request
	at org.postgresql.core.v3.QueryExecutorImpl.receiveErrorResponse(QueryExecutorImpl.java:2725) ~[postgresql-42.7.3.jar:42.7.3]
	at org.postgresql.core.v3.QueryExecutorImpl.processResults(QueryExecutorImpl.java:2412) ~[postgresql-42.7.3.jar:42.7.3]
```

{{% notice info "Note" %}}
Always assume that all DB calls never return or are very slow and design accordingly.
{{% /notice %}}

You can further look at optimizing the query with help of indexes or introducing caching.

You can enable `show-sql` to view all the db queries

```bash
spring.jpa.show-sql=true
```

### Memory Leak & CPU Spike

{{% notice note "Problem" %}}
You have developed your service on your laptop and tested in local kubernetes instance. 
Your kubernetes admin calls you to inform that on production kubernetes your pods are restarting frequently. What do you do?
{{% /notice %}}

Memory leaks are always hard to debug, a badly written method can cause spike in heap memory usage causing lot of GC (garbage collection) which are **stop of the world events**. 

With kubernetes you can define resource limits that kill the pod if tries to use more resources than allocated.

```yaml
resources:
    requests:
      cpu: "250m"
      memory: "250Mi"
    limits:
      cpu: "2"
      memory: "380Mi"
```

Now when you invoke the api that causes a memory spike, the pod will be killed (OOMKilled) and a new pod brought up.

![](img01.png)

![](img03.png)

![](img04.png)

{{% notice info "Note" %}}
For an OutOfMemoryError the pod doesn't necessarily kill the pod unless some health check is configured. Pod will still remain in running state despite the OOM error.
Only the resource limits defined determine when the pod gets killed.
{{% /notice %}}

```
Exception in thread "http-nio-8080-exec-1" java.lang.OutOfMemoryError: Java heap space
```

### Response Payload Size

{{% notice note "Problem" %}}
Your rest api returns list of customer records, However as more customers are added in production the size of response becomes bigger & bigger and slows down the request-response times.
{{% /notice %}}

Always add **pagination** support and avoid returning all the data in a single response. Data may grow later causing response size to get bigger over a period of time.

Enable gzip compression which also reduce the size of response payload. 

```bash
server.compression.enabled=true
 
# Minimum response when compression will kick in
server.compression.min-response-size=512
 
# Mime types that should be compressed
server.compression.mime-types=text/xml, text/plain, application/json
```

You can also consider using **GraphQL** so that client can request for only the data it needs

You can also change the protocol to http2 to get more benefits like multiplexing many requests over single tcp connection.

```bash
server.http2.enabled=true
```

{{% notice info "Note" %}}
Always try to reduce the size of the response payload, send only the data required instead of the whole payload. Use pagination for data records and gzip payload to reduce the size.
{{% /notice %}}

### API versioning

{{% notice note "Problem" %}}
A new team member has updated an existing API & introduced a new feature that was used by many downstream applications, however a bug got introduced and now all the downstream api are failing.
{{% /notice %}}

Always look at versioning your api instead of updating existing api that are used by downstream services. This contains the **blast radius** of any bug.

eg: `/api/v1/customers` being the old api and `/api/v2/customers` being the new api

{{% notice info "Note" %}}
Backward compatibility is very important, specially when services rollback to older versions in distributed systems. Always work with versioned API if there are major changes or new features being introduced.
{{% /notice %}}

### Rate Limiter

{{% notice note "Problem" %}}
A particular customer of your service is over using the API to the extent that other users are unable to get a response on the API
{{% /notice %}}

Look at implementing rate limiting per customer. Rate limiting can be implemented at gateway level or at application level.

[http://gitorko.github.io/post/spring-traefik-rate-limit](http://gitorko.github.io/post/spring-traefik-rate-limit)

### Retry

### Circuit Breaker Pattern

### Bulk Head Pattern

![](img05.png)

### Observability

### Chaos Monkey


### Other Failures

Other aspects of distributed system to consider for points of failure

1. Primary DB failure - Active-Active setup vs Active-Passive setup
2. Secondary DB replication failure
3. Queue failures
4. Network failures
5. External Systems can go down
6. Service nodes can go down so your service must be resilient to this
7. Cache invalidation/eviction (TTL) failure
8. Load Balancer failures
9. Datacenter failure for one region

## Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project57/main/src/main/java/com/demo/project57/Main.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project57/main/src/main/java/com/demo/project57/controller/HomeController.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project57/main/src/main/java/com/demo/project57/service/CustomerService.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project57/main/src/main/java/com/demo/project57/service/CustomerAsyncService.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project57/main/src/main/resources/application.yaml" >}}

## Postman

![](img02.png)

Import the postman collection to postman

[Postman Collection](https://raw.githubusercontent.com/gitorko/project57/main/postman/Project57.postman_collection.json)

## Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project57/main/README.md" >}}

## References

[https://resilience4j.readme.io/docs](https://resilience4j.readme.io/docs)
