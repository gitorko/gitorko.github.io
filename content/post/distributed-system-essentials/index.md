---
title: 'Distributed System Essentials'
description: 'Distributed System Essentials'
summary: 'Distributed System Essentials'
date: '2024-06-20'
aliases: [/points-of-failure/, /distributed-system-essentials/]
author: 'Arjun Surendra'
categories: [Distributed-System]
tags: [fail-fast, resilience4j, kubernetes, spring, postgres, bulkhead, rate-limit, spring-boot]
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

Spring also provides `spring.mvc.async.request-timeout` that ensures REST APIs can timeout after the configurable amount of time.

{{% notice info "Note" %}}
Always assume the functions/api will take forever and may never complete, design system accordingly by fencing the methods.
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

Throughput (requests served per second) of a single server depends on following

1. Number of tomcat threads 
2. Server hardware (CPU, Memory, SSD, Network Bandwidth) 
3. Type of task (IO intensive vs CPU intensive)

If you have 200 threads (BIO) and all request response on average take 1 second (latency) to complete then your server can handle 200 requests per second.
When there are IO intensive tasks which cause threads to wait and context switching takes place, throughput calculation becomes tricky and needs to be approximated.

{{% notice info "Note" %}}
Benchmark the system on a varied load to arrive at the peek throughput the system can handle.
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

If you are using WebClient then use Mono.timeout() or Flux.timeout() methods

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

### Long-Running Database Query

{{% notice note "Problem" %}}
DBA call you up and informs you that there is a long-running query in your service. What do you do?
{{% /notice %}}

Long-running queries often slow down the entire system.
To test this we explicitly slow down a query with pg_sleep function.

We set timeout on the transaction `@Transactional(timeout = 5)` to ensure that long-running query doesn't impact the entire system, after 5 seconds if the query doesn't return result an exception is thrown.

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
Always assume that all DB calls never return or are long-running and design accordingly.
{{% /notice %}}

You can further look at optimizing the query with help of indexes to avoid **full table scan** or introducing caching.

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
      memory: "500Mi"
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

### Bulk Head Pattern

{{% notice note "Problem" %}}
Thread pools are shared, a runway function is occupying the thread pool 100% and not letting other tasks execute. What do you do?
{{% /notice %}}

Bulkhead defines maximum number of concurrent calls allowed to be executed in a given timeframe. This prevents failures in a system/API from affecting other systems/APIs

![](img05.png)

The `@Bulkhead` is the annotation used to enable bulkhead on an API call. This can be applied at the method level or a class level. If applied at the class level, it applies to all public methods.

1. `max-concurrent-calls` - Number of concurrent calls allowed
2. `max-wait-duration` - Wait for 10ms before failing in case of the limit breach

```bash
ab -n 10 -c 10 http://localhost:8080/api/job10/10
```

```
Complete requests:      10
Failed requests:        7
   (Connect: 0, Receive: 0, Length: 7, Exceptions: 0)
Non-2xx responses:      7
```

**Rate Limit vs Bulk Head**

1. rate-limit - Allow this api to run only 10 requests per min.
2. bulk-head - Allow this api to use only 10 threads from the pool per min to run. Rest of threads will be available for other API.

### Rate Limiter

{{% notice note "Problem" %}}
A particular api of your service is overused due to a wrong retry logic in a client which just keeps spamming your server on that single api.
{{% /notice %}}

Look at implementing rate limiting. Rate limiting can be implemented at gateway level or at application level. It helps prevent Denial of Service attacks.

For rate limiting implementation at gateway level refer

[http://gitorko.github.io/post/spring-traefik-rate-limit](http://gitorko.github.io/post/spring-traefik-rate-limit)

The `@RateLimiter` is the annotation used to rate-limit an API call and applied at the method or class levels. If applied at the class level, it applies to all public methods

1. `timeout-duration` - default wait time a thread waits for a permission
2. `limit-refresh-period` - time window to count the requests
3. `limit-for-period` - number of requests or method invocations are allowed in the above limit-refresh-period

```bash
ab -n 10 -c 10 http://localhost:8080/api/job11/1
```

```
Complete requests:      10
Failed requests:        5
```

{{% notice info "Note" %}}
Always assume that your api will be invoked by clients more than they are intended to be invoked due to wrong retry configuration.
{{% /notice %}}

### Retry

### Circuit Breaker Pattern

### Health Check

### Observability

### Logging

{{% notice note "Problem" %}}
Kubernetes pods are ephemeral, you dont have access to history logs that are written to console.
{{% /notice %}}

1. Enable file logging
2. Enable rolling of log file
3. Enable trace-id in log file
4. Enable GC logging
5. Enable async logging (does come with risk of loosing few log messages)

On kubernetes write the log to a persistent volume else you will loose the logs on pod restart

```bash
logging:
  file:
    name: project57-app.log
  logback:
    rollingpolicy:
      file-name-pattern: logs/%d{yyyy-MM, aux}/app.%d{yyyy-MM-dd}.%i.log
      max-file-size: 100MB
      total-size-cap: 10GB
      max-history: 10
  level:
    root: info
```

### JVM tuning

{{% notice note "Problem" %}}
Users are reporting that once in a while the API response is really long and it returns back to normal response time in a short while. What do you do?
{{% /notice %}}

Garbage collection can impact response times as GC is stop of the world event. When major GC happens it pauses all threads which might impact response time for critical api.

Tune your JVM and enable logging and monitoring (actuator + prometheus) on the GC

1. `-Xms, -Xmx` - Places boundaries on the heap size to increase the predictability of garbage collection. The heap size is limited in replica servers so that even Full GCs do not trigger SIP retransmissions. -Xms sets the starting size to prevent pauses caused by heap expansion.
2. `-XX:+UseG1GC` - Use the Garbage First (G1) Collector.
3. `-XX:MaxGCPauseMillis` -  Sets a target for the maximum GC pause time. This is a soft goal, and the JVM will make its best effort to achieve it.
4. `-XX:ParallelGCThreads` - Sets the number of threads used during parallel phases of the garbage collectors. The default value varies with the platform on which the JVM is running.
5. `-XX:ConcGCThreads` - Number of threads concurrent garbage collectors will use. The default value varies with the platform on which the JVM is running.
6. `-XX:InitiatingHeapOccupancyPercent` - Percentage of the (entire) heap occupancy to start a concurrent GC cycle. GCs that trigger a concurrent GC cycle based on the occupancy of the entire heap and not just one of the generations, including G1, use this option. A value of 0 denotes 'do constant GC cycles'. The default value is 45.
7. `-XX:HeapDumpOnOutOfMemoryError` - Will dump the heap to file in case of out of memory error.

```bash
'-server'
'-Xms250m',
'-Xmx500m',
'-XX:+HeapDumpOnOutOfMemoryError'
'-XX:+UseG1GC',
'-XX:MaxGCPauseMillis=200',
'-XX:ParallelGCThreads=20',
'-XX:ConcGCThreads=5',
'-XX:InitiatingHeapOccupancyPercent=70',
'-Xlog:gc*=info:file=project57-gc.log:time,uptime,level,tags:filecount=5,filesize=100m
```

![](img08.png)

### Server Startup Time

{{% notice note "Problem" %}}
Your notice your server startup time is slow, it takes 10 sec for the server to startup. What do you do?
{{% /notice %}}

You can enable lazy initialization, Spring won’t create all beans on startup it will inject no dependencies until that bean is needed

You can check if autoconfigured beans are being set and disable them if not required.

```bash
logging.level.org.springframework.boot.autoconfigure=DEBUG
```

Disable JMX beans to save on time

```bash
spring.jmx.enabled=false
```

```bash
spring.main.lazy-initialization=true
```

Ahead of Time (AOT) Compilation creates a native binary image that doesn't require Java to run.
It will increase startup time and reduce memory footprint.
It optimizes by doing static analysis, removal of unused code, creating fixed classpath, etc.

### Security

{{% notice note "Problem" %}}
You have ensured that you don't print any customer information in logs, however the heapdump file that was shared in a ticket now exposes passwords to any user without access. What do you do?
{{% /notice %}}

You have ensured that 

1. No credit card numbers in logs.
2. No passwords in logs.
3. No User personal information in logs.
4. No personal email in the logs.
5. Permissions to production is restricted to few people by Authentication & Authorization.
6. Salt has been added to password before storing it.

However heap dump file is one area that can leak passwords if the file is shared.

Trigger a password generation request and at the same time take a heap dump. You will see the password in plain text.

```bash
curl --location 'http://localhost:8080/api/job15/60'
```

![](img09.png)

{{% notice info "Note" %}}
Heap dump files also need to protected with password similar to production data access.
{{% /notice %}}

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
10. Chaos Monkey testing
11. CDN usage
12. Audit Logging

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
