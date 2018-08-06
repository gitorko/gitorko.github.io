---
title: Apache Kafka with Spring - Producer & Consumer
date: 2018-08-06 00:00:00
tags: kafka,spring,producer,consumer,stream
---

We will learn how to integrate kafka server with spring and write a producer consumer application.

<!-- more -->

## Kafka

Kafka is a distributed & fault-tolerant, scalable stream processing system. Similar to spring rest template or jdbc template which abstracts the rest/jdbc calls spring provides kafka template also abstract the api to kafka server. There is an even higher level of abstraction provided by spring cloud which lets you integrate with kafka,rabbitmq and other messaging systems, however for now we will look at spring kafka template only.

The first step is to get your kafka instance up. You can choose to do the deployment by following [Quick Start](https://kafka.apache.org/quickstart) or use docker to stand up your kafka server. Kafka requires zookeeper hence using docke compose we will orchestrate both these servers. Create a docker-compose.yml file.

```yml
version: '2'
services:
  zookeeper:
    container_name: zookeeper
    image: 'bitnami/zookeeper:latest'
    ports:
      - 2181:2181
    environment:
      - ALLOW_ANONYMOUS_LOGIN=yes
    networks:
      - kafkanet
  kafkaserver:
    hostname: kafkaserver
    container_name: kafkaserver
    image: 'bitnami/kafka:latest'
    ports:
      - 9092:9092
    depends_on:
      - zookeeper
    networks:
      - kafkanet      
    environment:
      - KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181
      - KAFKA_ADVERTISED_HOST_NAME=kafkaserver
      - ALLOW_PLAINTEXT_LISTENER=yes
    links:
      - zookeeper:zookeeper
networks:
  kafkanet:
    driver: bridge
```

Ensure the C:\Windows\System32\drivers\etc\hosts file has these 2 entries.

```bash
127.0.0.1 zookeeper
127.0.0.1 kafkaserver
```

Run the command:

```bash
docker-compose up -d

Creating zookeeper ... done
Creating kafkaserver ... done
```
Once the kafka server is started you can create a topic. If you have kitematic then you can open the terminal, else you can enter the following command

```bash
docker exec -it kafkaserver /bin/bash
```

Then run the command to create topic

```bash
/opt/bitnami/kafka/bin/kafka-topics.sh --create --zookeeper zookeeper:2181 --replication-factor 1 --partitions 1 --topic mytopic
```
Create a maven project using [start.spring.io](https://start.spring.io/)

{% asset_img image-01.PNG %}

Add the jackson dependency to the pom.xml

```xml
<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-databind</artifactId>
</dependency>
```

Create a Main.java file

```java
import java.util.Arrays;
import java.util.List;
import java.util.Random;
import java.util.concurrent.TimeUnit;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.stereotype.Component;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.extern.slf4j.Slf4j;

@SpringBootApplication
@Slf4j
public class Main {

    @Autowired
    private KafkaTemplate<String, User> kafkaTemplate;

    public static void main(String[] args) {
        SpringApplication.run(Main.class, args);
    }

    @Component
    class MainRunner implements ApplicationRunner {

        public void run(ApplicationArguments args) throws Exception {
            List<String> users = Arrays.asList("david","john","raj","peter");
            List<String> company = Arrays.asList("ibm","apple","google","vmware");
            while (true) {
                kafkaTemplate.send("mytopic", new User(users.get(new Random().nextInt(users.size())),company.get(new Random().nextInt(company.size())),new Random().nextInt(100)));
                TimeUnit.SECONDS.sleep(5);
            }
        }
    }

    @KafkaListener(topics = "mytopic")
    public void receiveTopic1(ConsumerRecord<?, ?> userRecord) {
        log.info("Received : {} ", userRecord);
    }
}

@Data
@AllArgsConstructor
class User{
    String userName,company;
    int salary;
}
```

Enter the following properites in application.properties file under src/main/resources. This basically tell your application which kafka server to connect to, how to serialzie the key, how to deserialize the value. The group id of your client etc.

```yml
spring.kafka.consumer.group-id=test-group
spring.kafka.consumer.auto-offset-reset=earliest
spring.kafka.bootstrap-servers=localhost:9092
spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.value-serializer=org.springframework.kafka.support.serializer.JsonSerializer
spring.main.web-application-type=NONE
```

Run the project. It will print the list of ProducerConfig and ConsumerConfig and then the producer sends a event and consumer consumes the event. 

```bash
2018-08-06 15:17:29.294  INFO 25300 --- [           main] kafademo.Main                            : Starting Main on ASURENDRA-W01 with PID 25300 (C:\Code\kafka-demo\target\classes started by asurendra in C:\Code\kafka-demo)
2018-08-06 15:17:29.298  INFO 25300 --- [           main] kafademo.Main                            : No active profile set, falling back to default profiles: default
2018-08-06 15:17:29.351  INFO 25300 --- [           main] s.c.a.AnnotationConfigApplicationContext : Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@402e37bc: startup date [Mon Aug 06 15:17:29 IST 2018]; root of context hierarchy
2018-08-06 15:17:29.955  INFO 25300 --- [           main] trationDelegate$BeanPostProcessorChecker : Bean 'org.springframework.kafka.annotation.KafkaBootstrapConfiguration' of type [org.springframework.kafka.annotation.KafkaBootstrapConfiguration$$EnhancerBySpringCGLIB$$cae95a95] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)
2018-08-06 15:17:30.373  INFO 25300 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2018-08-06 15:17:30.398  INFO 25300 --- [           main] o.s.c.support.DefaultLifecycleProcessor  : Starting beans in phase 2147483547
2018-08-06 15:17:30.487  INFO 25300 --- [           main] o.a.k.clients.consumer.ConsumerConfig    : ConsumerConfig values: 
	auto.commit.interval.ms = 5000
	auto.offset.reset = earliest
	bootstrap.servers = [localhost:9092]
	check.crcs = true
	client.id = 
	connections.max.idle.ms = 540000
	enable.auto.commit = true
	exclude.internal.topics = true
	fetch.max.bytes = 52428800
	fetch.max.wait.ms = 500
	fetch.min.bytes = 1
	group.id = test-group
	heartbeat.interval.ms = 3000
	interceptor.classes = null
	internal.leave.group.on.close = true
	isolation.level = read_uncommitted
	key.deserializer = class org.apache.kafka.common.serialization.StringDeserializer
	max.partition.fetch.bytes = 1048576
	max.poll.interval.ms = 300000
	max.poll.records = 500
	metadata.max.age.ms = 300000
	metric.reporters = []
	metrics.num.samples = 2
	metrics.recording.level = INFO
	metrics.sample.window.ms = 30000
	partition.assignment.strategy = [class org.apache.kafka.clients.consumer.RangeAssignor]
	receive.buffer.bytes = 65536
	reconnect.backoff.max.ms = 1000
	reconnect.backoff.ms = 50
	request.timeout.ms = 305000
	retry.backoff.ms = 100
	sasl.jaas.config = null
	sasl.kerberos.kinit.cmd = /usr/bin/kinit
	sasl.kerberos.min.time.before.relogin = 60000
	sasl.kerberos.service.name = null
	sasl.kerberos.ticket.renew.jitter = 0.05
	sasl.kerberos.ticket.renew.window.factor = 0.8
	sasl.mechanism = GSSAPI
	security.protocol = PLAINTEXT
	send.buffer.bytes = 131072
	session.timeout.ms = 10000
	ssl.cipher.suites = null
	ssl.enabled.protocols = [TLSv1.2, TLSv1.1, TLSv1]
	ssl.endpoint.identification.algorithm = null
	ssl.key.password = null
	ssl.keymanager.algorithm = SunX509
	ssl.keystore.location = null
	ssl.keystore.password = null
	ssl.keystore.type = JKS
	ssl.protocol = TLS
	ssl.provider = null
	ssl.secure.random.implementation = null
	ssl.trustmanager.algorithm = PKIX
	ssl.truststore.location = null
	ssl.truststore.password = null
	ssl.truststore.type = JKS
	value.deserializer = class org.apache.kafka.common.serialization.StringDeserializer

2018-08-06 15:17:30.558  INFO 25300 --- [           main] o.a.kafka.common.utils.AppInfoParser     : Kafka version : 1.0.2
2018-08-06 15:17:30.558  INFO 25300 --- [           main] o.a.kafka.common.utils.AppInfoParser     : Kafka commitId : 2a121f7b1d402825
2018-08-06 15:17:30.564  INFO 25300 --- [           main] o.s.s.c.ThreadPoolTaskScheduler          : Initializing ExecutorService 
2018-08-06 15:17:30.577  INFO 25300 --- [           main] kafademo.Main                            : Started Main in 1.648 seconds (JVM running for 2.661)
2018-08-06 15:17:30.583  INFO 25300 --- [           main] o.a.k.clients.producer.ProducerConfig    : ProducerConfig values: 
	acks = 1
	batch.size = 16384
	bootstrap.servers = [localhost:9092]
	buffer.memory = 33554432
	client.id = 
	compression.type = none
	connections.max.idle.ms = 540000
	enable.idempotence = false
	interceptor.classes = null
	key.serializer = class org.apache.kafka.common.serialization.StringSerializer
	linger.ms = 0
	max.block.ms = 60000
	max.in.flight.requests.per.connection = 5
	max.request.size = 1048576
	metadata.max.age.ms = 300000
	metric.reporters = []
	metrics.num.samples = 2
	metrics.recording.level = INFO
	metrics.sample.window.ms = 30000
	partitioner.class = class org.apache.kafka.clients.producer.internals.DefaultPartitioner
	receive.buffer.bytes = 32768
	reconnect.backoff.max.ms = 1000
	reconnect.backoff.ms = 50
	request.timeout.ms = 30000
	retries = 0
	retry.backoff.ms = 100
	sasl.jaas.config = null
	sasl.kerberos.kinit.cmd = /usr/bin/kinit
	sasl.kerberos.min.time.before.relogin = 60000
	sasl.kerberos.service.name = null
	sasl.kerberos.ticket.renew.jitter = 0.05
	sasl.kerberos.ticket.renew.window.factor = 0.8
	sasl.mechanism = GSSAPI
	security.protocol = PLAINTEXT
	send.buffer.bytes = 131072
	ssl.cipher.suites = null
	ssl.enabled.protocols = [TLSv1.2, TLSv1.1, TLSv1]
	ssl.endpoint.identification.algorithm = null
	ssl.key.password = null
	ssl.keymanager.algorithm = SunX509
	ssl.keystore.location = null
	ssl.keystore.password = null
	ssl.keystore.type = JKS
	ssl.protocol = TLS
	ssl.provider = null
	ssl.secure.random.implementation = null
	ssl.trustmanager.algorithm = PKIX
	ssl.truststore.location = null
	ssl.truststore.password = null
	ssl.truststore.type = JKS
	transaction.timeout.ms = 60000
	transactional.id = null
	value.serializer = class org.springframework.kafka.support.serializer.JsonSerializer

2018-08-06 15:17:30.605  INFO 25300 --- [           main] o.a.kafka.common.utils.AppInfoParser     : Kafka version : 1.0.2
2018-08-06 15:17:30.605  INFO 25300 --- [           main] o.a.kafka.common.utils.AppInfoParser     : Kafka commitId : 2a121f7b1d402825
2018-08-06 15:17:30.693  INFO 25300 --- [ntainer#0-0-C-1] o.a.k.c.c.internals.AbstractCoordinator  : [Consumer clientId=consumer-1, groupId=test-group] Discovered group coordinator kafkaserver:9092 (id: 2147482646 rack: null)
2018-08-06 15:17:30.697  INFO 25300 --- [ntainer#0-0-C-1] o.a.k.c.c.internals.ConsumerCoordinator  : [Consumer clientId=consumer-1, groupId=test-group] Revoking previously assigned partitions []
2018-08-06 15:17:30.697  INFO 25300 --- [ntainer#0-0-C-1] o.s.k.l.KafkaMessageListenerContainer    : partitions revoked: []
2018-08-06 15:17:30.698  INFO 25300 --- [ntainer#0-0-C-1] o.a.k.c.c.internals.AbstractCoordinator  : [Consumer clientId=consumer-1, groupId=test-group] (Re-)joining group
2018-08-06 15:17:30.723  INFO 25300 --- [ntainer#0-0-C-1] o.a.k.c.c.internals.AbstractCoordinator  : [Consumer clientId=consumer-1, groupId=test-group] Successfully joined group with generation 3
2018-08-06 15:17:30.725  INFO 25300 --- [ntainer#0-0-C-1] o.a.k.c.c.internals.ConsumerCoordinator  : [Consumer clientId=consumer-1, groupId=test-group] Setting newly assigned partitions [mytopic-0]
2018-08-06 15:17:30.730  INFO 25300 --- [ntainer#0-0-C-1] o.s.k.l.KafkaMessageListenerContainer    : partitions assigned: [mytopic-0]
2018-08-06 15:17:30.774  INFO 25300 --- [ntainer#0-0-C-1] kafademo.Main                            : Received : ConsumerRecord(topic = mytopic, partition = 0, offset = 4, CreateTime = 1533548798059, serialized key size = -1, serialized value size = 49, headers = RecordHeaders(headers = [RecordHeader(key = __TypeId__, value = [107, 97, 102, 97, 100, 101, 109, 111, 46, 85, 115, 101, 114])], isReadOnly = false), key = null, value = {"userName":"john","company":"apple","salary":85}) 
2018-08-06 15:17:30.788  INFO 25300 --- [ntainer#0-0-C-1] kafademo.Main                            : Received : ConsumerRecord(topic = mytopic, partition = 0, offset = 5, CreateTime = 1533548850765, serialized key size = -1, serialized value size = 49, headers = RecordHeaders(headers = [RecordHeader(key = __TypeId__, value = [107, 97, 102, 97, 100, 101, 109, 111, 46, 85, 115, 101, 114])], isReadOnly = false), key = null, value = {"userName":"raj","company":"vmware","salary":72}) 
2018-08-06 15:17:35.786  INFO 25300 --- [ntainer#0-0-C-1] kafademo.Main                            : Received : ConsumerRecord(topic = mytopic, partition = 0, offset = 6, CreateTime = 1533548855772, serialized key size = -1, serialized value size = 51, headers = RecordHeaders(headers = [RecordHeader(key = __TypeId__, value = [107, 97, 102, 97, 100, 101, 109, 111, 46, 85, 115, 101, 114])], isReadOnly = false), key = null, value = {"userName":"peter","company":"vmware","salary":19}) 
2018-08-06 15:17:40.783  INFO 25300 --- [ntainer#0-0-C-1] kafademo.Main                            : Received : ConsumerRecord(topic = mytopic, partition = 0, offset = 7, CreateTime = 1533548860773, serialized key size = -1, serialized value size = 46, headers = RecordHeaders(headers = [RecordHeader(key = __TypeId__, value = [107, 97, 102, 97, 100, 101, 109, 111, 46, 85, 115, 101, 114])], isReadOnly = false), key = null, value = {"userName":"raj","company":"ibm","salary":44}) 
```

What we have done so far is simple produce consumer similar to other JMS systems. However the next step is to use Streams feature of kafka to achive something similar to map reduce functionality. We will look at that in the next post.