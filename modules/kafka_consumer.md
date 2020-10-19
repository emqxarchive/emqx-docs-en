# Kafka Consumer Group

The Kafka consumer group uses external Kafka as a message queue, which can convert consumer messages from Kafka into MQTT messages and publish them in emqx.

Set up the Kafka environment, taking MacOS X as an example:

```bash
$ wget http://apache.claz.org/kafka/2.3.1/kafka_2.12-2.3.1.tgz

$ tar -xzf kafka_2.12-2.3.1.tgz

$ cd kafka_2.12-2.3.1

# Start Zookeeper
$ ./bin/zookeeper-server-start.sh config/zookeeper.properties
# Start Kafka
$ ./bin/kafka-server-start.sh config/server.properties
```

{% hint style="danger" %}

Kafka consumer groups do not support Kafka versions below 0.9

Before creating resources, you need to create Kafka topics in advance, otherwise an error will be prompted

{% endhint %}

Create Kafka topic:

```bash
$ ./bin/kafka-topics.sh --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic testTopic --create
```
The Kafka consumer group module can be opened through the dashboard page

Open [EMQ X Dashboard](http://127.0.0.1:18083/#/modules) and select the "Resources" tab on the left:

Select Kafka consumer group module:

![](./assets/kafka_consumer1.png)


Fill in the relevant parameters:

![](./assets/kafka_consumer3.png)

1). Kafka server address

2). Kafka consumer connection pool size

3). Kafka subscription topic

4). MQTT message subject

5). MQTT theme service quality

6). Kafka Max Bytes (the maximum number of bytes of messages consumed each time from Kafka)

7). Kafka Offset Reset Policy (reset Offset policy, reset_to_latest | reset_by_subdcriber)

7). Is Kafka consumer reconnected?

After clicking Add, the module is added:

![](./assets/kafka_consumer4.png)

The resource has been created, now use Dashboard's websocket tool to subscribe to the MQTT topic "TestTopic":

![](./assets/kafka_consumer5.png)

Use the kafka command line to produce a message:

```bash
./bin/kafka-console-producer.sh --broker-list localhost:9092 --topic TestTopic
```

![](./assets/kafka_consumer6.png)

The websocket tool of Dashboard received the message "hello-kafka" produced by Kafka:

![](./assets/kafka_consumer7.png)