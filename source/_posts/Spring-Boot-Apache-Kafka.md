---
title: Spring Boot & Apache Kafka
date: 2018-08-06 00:00:00
tags: kafka,spring,producer,consumer,stream
---

Spring boot integration with kafka server to create a producer consumer application.

<!-- more -->

<!-- toc -->

## Kafka

Kafka is a distributed & fault-tolerant, scalable stream processing system. You can use kafka as publisher-subscriber messaging system, you can use kafka as stream processing system that reacts to event in realtime, you can use kafka as a store for data.

Similar to spring rest template or jdbc template which abstracts the rest/jdbc calls spring provides kafka template which provides high level abstraction to interact with kafka. There is an even higher level of abstraction provided by spring cloud which lets you integrate with kafka,rabbitmq and other messaging systems, however for now we will look at spring kafka template only.

### Setup

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
  kafkaserver:
    hostname: kafkaserver
    container_name: kafkaserver
    image: 'bitnami/kafka:latest'
    ports:
      - 9092:9092
    depends_on:
      - zookeeper
    environment:
      - KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181
      - KAFKA_ADVERTISED_HOST_NAME=kafkaserver
      - ALLOW_PLAINTEXT_LISTENER=yes
    links:
      - zookeeper:zookeeper
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

### Publisher Subscriber Application

Add the jackson dependency to the pom.xml

```xml
<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-databind</artifactId>
</dependency>
```

Create a Main.java file

```java
package kafademo;

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
import lombok.NoArgsConstructor;
import lombok.extern.slf4j.Slf4j;

@SpringBootApplication
@Slf4j
public class Main {

    private final static String TOPIC_NAME = "mytopic";

    public static void main(String[] args) {
        SpringApplication.run(Main.class, args);
    }

    @Component
    class MainRunner implements ApplicationRunner {

        @Autowired
        private KafkaTemplate<String, User> kafkaTemplate;

        public void run(ApplicationArguments args) throws Exception {
            sendDataToStream();
        }

        public void sendDataToStream() throws InterruptedException {
            List<String> users = Arrays.asList("david", "john", "raj", "peter");
            List<String> company = Arrays.asList("ibm", "apple", "google", "vmware");
            while (true) {
                kafkaTemplate.send(TOPIC_NAME, new User(users.get(new Random().nextInt(users.size())),
                        company.get(new Random().nextInt(company.size())), new Random().nextInt(100)));
                TimeUnit.SECONDS.sleep(15);
            }
        }

        @KafkaListener(topics = TOPIC_NAME, groupId = "test-group")
        public void topicConsumer(ConsumerRecord<?, ?> userRecord) {
            log.info("Received : {} ", userRecord);
        }
    }
}

@Data
@AllArgsConstructor
@NoArgsConstructor
class User {
    String userName, company;
    int salary;
}
```

Enter the following properites in application.properties file under src/main/resources. This basically tell your application which kafka server to connect to, how to serialzie the key, how to deserialize the value. The group id of your client which uses group management to assign topic partitions to consumers, auto-offset-reset=earliest ensures the new consumer group will get the messages we just sent. The ProducerFactory and ConsumerFactory read these properites and create the instances.

application.properties

```yml
spring.kafka.consumer.group-id=test-group
spring.kafka.consumer.auto-offset-reset=earliest
spring.kafka.bootstrap-servers=localhost:9092
spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.value-serializer=org.springframework.kafka.support.serializer.JsonSerializer
spring.main.web-application-type=NONE
```

We can have multiple kakfa listner for a topic with different group id,A consumer can listen to more than one topic. We had created the topic 'mytopic' with only one partition. For a topic with multiple partitions, @KafkaListener can explicitly subscribe to a particular partition of a topic with an initial offset.

```java
@KafkaListener(topics = "topic1", group = "group1")
@KafkaListener(topics = "topic1,topic2", group = "group1")
@KafkaListener(topicPartitions = @TopicPartition(topic = "topic1",
  partitionOffsets = {
    @PartitionOffset(partition = "0", initialOffset = "0"), 
    @PartitionOffset(partition = "2", initialOffset = "0")
}))
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

What we have done so far is simple produce consumer similar to other JMS systems. However the next step is to use Streams feature of kafka to achive something similar to map reduce functionality. 

### Kafka Streams

When you use other projects like apache spark, storm,flink you write code and copy the jar to the nodes where the actual work happens. With the introduction of kafka stream you can now write your processing logic for streams and then it can run anywhere the jar can run. KafkaStreams enables us to consume from Kafka topics, analyze or transform data, and potentially, send it to another Kafka topic.

Now modify your code to add kafka stream functionality to count the employees in a company as and when the employees get added. The method processStream prints the employee count. The method sendDataToStream sends new employee events to the stream.

```java
import java.util.Arrays;
import java.util.List;
import java.util.Properties;
import java.util.Random;
import java.util.concurrent.TimeUnit;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.Consumed;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.StreamsBuilder;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.kstream.KStream;
import org.apache.kafka.streams.kstream.KTable;
import org.apache.kafka.streams.kstream.Serialized;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.kafka.annotation.KafkaStreamsDefaultConfiguration;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.kafka.support.serializer.JsonSerde;
import org.springframework.stereotype.Component;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.extern.slf4j.Slf4j;

@SpringBootApplication
@Slf4j
public class Main {

    private final static String TOPIC_NAME = "mytopic";

    public static void main(String[] args) {
        SpringApplication.run(Main.class, args);
    }

    @Component
    class MainRunner implements ApplicationRunner {

        @Autowired
        private KafkaTemplate<String, User> kafkaTemplate;

        @Bean(name = KafkaStreamsDefaultConfiguration.DEFAULT_STREAMS_CONFIG_BEAN_NAME)
        public Properties kStreamsConfigs() {
            Properties props = new Properties();
            props.put(StreamsConfig.APPLICATION_ID_CONFIG, "countstream");
            props.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
            return props;
        }

        public void run(ApplicationArguments args) throws Exception {
            processStream();
            sendDataToStream();
        }

        public void sendDataToStream() throws InterruptedException {
            List<String> users = Arrays.asList("david", "john", "raj", "peter");
            List<String> company = Arrays.asList("ibm", "apple", "google", "vmware");
            while (true) {
                kafkaTemplate.send(TOPIC_NAME, new User(users.get(new Random().nextInt(users.size())),
                        company.get(new Random().nextInt(company.size())), new Random().nextInt(100)));
                TimeUnit.SECONDS.sleep(15);
            }
        }

        public void processStream() {
            StreamsBuilder builder = new StreamsBuilder();
            KStream<String, User> streamOfUsers = builder.stream(TOPIC_NAME,
                    Consumed.with(Serdes.String(), new JsonSerde<>(User.class)));

            //streamOfUsers.foreach((k, v) -> {
            //    log.info("key: " + k + " value: " + v.getCompany());
            //});

            KTable<String, Long> employeeCountByCompany = streamOfUsers
                    .map((k,v)-> new KeyValue<>(v.getCompany(), "0"))
                    .groupByKey(Serialized.with(Serdes.String(), Serdes.String()))
                    .count();
            employeeCountByCompany.foreach((w, c) -> log.info("Company: " + w + " -> " + c));

            KafkaStreams streams = new KafkaStreams(builder.build(), kStreamsConfigs());
            //streams.cleanUp();
            streams.start();
            Runtime.getRuntime().addShutdownHook(new Thread(streams::close));
        }

        @KafkaListener(topics = TOPIC_NAME, groupId = "test-group")
        public void topicConsumer(ConsumerRecord<?, ?> userRecord) {
            log.info("Received : {} ", userRecord);
        }
    }
}

@Data
@AllArgsConstructor
@NoArgsConstructor
class User {
    String userName, company;
    int salary;
}

```