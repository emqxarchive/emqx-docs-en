# EMQ X Broker

*EMQ X* (Erlang/Enterprise/Elastic MQTT Broker) is an open source IoT MQTT message broker based on the Erlang/OTP platform. Erlang/OTP is an excellent Soft-Realtime, Low-Latency and Distributed development platform. MQTT is a lightweight message exchange protocol using publish-subscribe pattern.

*EMQ X* is designed for massive clients access and realizes fast and low-latency message routing between massive physical network devices:

1.  Stable to host large-scale MQTT client connections, and a single-server node supports millions of connections.
2.  Distributed cluster, fast and low-latency message routing, and single-cluster supports tens of thousands of routes.
3.  Extensible, support customized plugins, such as authentication and other functions.
4.  Comprehensive IoT protocol support, including MQTT, MQTT-SN, CoAP, LwM2M, and other TCP/UDP based proprietary protocol.


**It is recommended that you read the documents listed below carefully before use. Other documents that is not listed can be viewed as needed:**

## Start
  - [Install](getting-started/install.md): download and installation steps for different operating systems and installation package types.
  - [Start EMQ X](getting-started/start.md): Start EMQ X and check the startup status.
  - [Dashboard](getting-started/dashboard.md): Manage EMQ X and online devices through Dashboard.

## Authentication
  - [Introduction to Authentication](advanced/auth.md): Choose the built-in plug-in, external database, JWT or HTTP service as the authentication data source to verify the legitimacy of the client connection.
  - [Publish and subscribe ACL](advanced/acl.md): Choose built-in plug-in, external database, or HTTP service as the ACL data source to verify the client's publish and subscribe permissions.
  - [Built-in ACL](modules/internal_acl.md): The built-in ACL may affect important functions, please learn more before using it.

## FAQ 

[FAQ ](faq/faq.md)) regularly collect and sort out EMQ X users' common problems and errors frequently encountered, such as the number limitation of topics, the difference between open source and enterprise editions, enterprise service charges, how does the open source version store data , etc..


## Community communication
 - [Resources](awesome/awesome.md): Community communication, including popular tutorials, project displays and other resources in the community.

## HTTP API

HTTP API is a frequently used function in IoT platform development and EMQ X operation and maintenance. HTTP API can achieve integration with external systems, such as querying and managing client information, proxy subscriptions, publishing messages, and creating rules.

  - [HTTP API](advanced/http-api.md): Include HTTP API access points and access authentication methods.
  - [Basic Information](advanced/http-api.md#endpoint-brokers): Get basic information such as EMQ X version and running status.
  - [Nodes](advanced/http-api.md#endpoint-nodes): Get EMQ X node information.
  - [Client](advanced/http-api.md#endpoint-clients): View online client information, support kicking out the client.
  - [Subscription Information](advanced/http-api.md#endpoint-subscriptions): View the list of subscription topics and subscription relationship.
  - [Routing](advanced/http-api.md#endpoint-routes): View the subscribed topics.
  - [Message publishing](advanced/http-api.md#endpoint-publish): Call EMQ X via HTTP to publish MQTT messages, which is a reliable way for the application to communicate with the client.
  - [Topic subscription](advanced/http-api.md#endpoint-subscribe): Dynamically manage the client's subscription list which don't require the client to initiate subscription/unsubscribe.
  - [Plugins](advanced/http-api.md#endpoint-plugins):  manage the status of plugins, with start and stop operations.

For more APIs, please check the list on the left.

## Rule engine

Through rule engine, you can filter, process, and forward/store messages to external data sources, including relational databases, message queues, Web services, and so on.

  - [Rule Engine](rule/rule-engine.md): The concept and basic usage of the rule engine.
  - [Create Rule](rule/rule-create.md): How to create a rule.
  - [Usage example](rule/rule-example.md#发送数据到-web-服务): A tutorial on how the rule engine uses various data sources.

## Data storage

It is a unique function for EMQ X Enterprise Edition. The data storage records the client's online and offline status, subscription relationship, offline messages, message content, and message receipts sent after the message arrives in various databases. Data storage includes runtime data and message data, and can retain data even after the service crashes and the client is abnormally offline.

  - [Data storage](backend/backend.md): Basic concepts and usage scenarios.
  - [Data storage configuration](backend/backend.md#redis-数据存储): Use different data sources for data storage.

## Message bridging

EMQ X Enterprise Edition bridges and forwards MQTT messages to Kafka, RabbitMQ, Pulsar, RocketMQ, MQTT Broker or other EMQ X nodes.

  - [MQTT bridge](bridge/bridge.md#mqtt-桥接): Realize cross-regional and cross-cluster deployment.
  - [RPC  bridge](bridge/bridge.md#rpc-桥接)
  - [Kafka  bridge](bridge/bridge.md#kafka-桥接)
  - [RabbitMQ  bridge](bridge/bridge.md#rabbitmq-桥接)
  - [Pulsar  bridge](bridge/bridge.md#pulsar-桥接)
  - [RocketMQ  bridge](bridge/bridge.md#rocketmq-桥接)


## Operation and maintenance deployment

It contains information such as official guidelines, best practices, etc.

 - [Device management](tutorial/device-management.md)
 - [System tune](tutorial/tune.md)
 - [Production deployment](tutorial/deploy.md)
 - [Prometheus Monitor and alert](tutorial/prometheus.md)
 - [Performance Testing](tutorial/benchmark.md)

## Protocol introduction
 - [MQTT protocol](development/protocol.md)
 - [MQTT-SN protocol](development/protocol.md#mqtt-sn-协议)
 - [LwM2M protocol](development/protocol.md#lwm2m-协议)
 - [Private TCP protocol](development/protocol.md#私有-tcp-协议)
