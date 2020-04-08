---
# 标题
title: 入门概念
# 编写日期
date: 2020-02-07 17:15:26
# 作者 Github 名称
author: wivwiv
# 关键字
keywords:
# 描述
description:
# 分类
category: 
# 引用
ref: undefinedBasic concept
---

# Basic concept

### What's EMQ X?

 EMQ X is an open-source, distributed MQTT messaging broker, it can support up to million level of concurrent MQTT connections. It can be used for connecting to any devices that support MQTT protocol, and it can also be used for delivering message from server side to client to realize the data collection of the Internet of Things equipment, and the operation and control of the equipment. 




### Why choose EMQ X？


Compared with other MQTT servers, EMQ X has the following advantages:

- After 100+ iterations, EMQ X is currently the most popular MQTT messaging middleware in the open source community, which has withstood severe tests in various strict production environments of the customer;
- EMQ X supports rich IoT protocols, including MQTT, MQTT-SN, CoAP, LwM2M, LoRaWAN, WebSocket, etc .;
- Optimized architecture design, support ultra-large-scale device connection. The enterprise version can support millions of MQTT connections, and the cluster can support tens of millions of MQTT connections;
- Easy to install and use;
- Flexible scalability to support some customized scenarios of the enterprise;
- With Local technical support services in China, quickly responding to customer needs through online channels such as WeChat and QQ;




### What is the relationship between EMQ X and IoT platform？

Typical IoT platforms include device hardware, data collection, data storage, analysis, Web/mobile applications, etc. EMQ X is located at the data collection layer, and interacts with hardware and data storage and analysis respectively. It is the core of the Internet of Things platform: the front-end hardware interacts with EMQ X located at the data collection layer through the MQTT protocol. After collecting data through EMQ X, through the data interface provided by EMQ X, it can save the data to the back-end persistent platform (various relational databases and NOSQL databases), or streaming data processing framework, etc. The results obtained by the upper-layer application through these data analysis are presented to end user.



## *How many products in EMQ X?*

**Tag:** [*Enterprise version*](tags.md#企业版)

EMQ X totally has [3 products.](https://www.emqx.io/products) Different products support different level of connections, features and services etc.

- EMQ X Broker: EMQ X open source version, supports the popular IoT protocols, such as MQTT, CoAP and LwM2M. It supports 100k level concurrent MQTT connections.
- EMQ X Enterprise: EMQ X enterprise version. It is based on the open source version, and adds data persistence (support Redis, MySQL, MongoDB or PostgreSQL), data bridge to Kafka, LoRaWAN support, EMQ X monitoring, Kubernetes deployment etc. It supports 1M level concurrent MQTT connections.
- EMQ X Platform: EMQ X Platform version is based on the Enterprise version，and support 10M level concurrent MQTT connections. We provide consulting service for complex IoT platforms, such as cross data center solutions. All kinds of services building an IoT platform can be provided, such as consulting, training, architect design, customized development, platform implementation, testing and operation.




### What is the relationship between EMQ X and NB-IoT, LoRAWAN？

**Tag:** [*NB-IoT*](tags.md#nb-iot)  [*LoRAWAN*](tags.md#lorawan)  [* Multi-protocol*](tags.md#多协议)

EMQ X is an open source MQTT broker, and MQTT is a protocol at the application layer on the TCP protocol stack; while NB-IoT and LoRAWAN are in the physical layer at the TCP protocol and are responsible for the transmission of physical signals. Therefore, they implement different functions at different levels of the TCP protocol stack.




### What are the advantages and weaknesses of the MQTT protocol compared to the HTTP protocol?  ?

**Tag:** [*Multi-protocol*](tags.md#多协议)

HTTP protocol is A stateless protocol. Each HTTP request is a TCP short connection, and each request needs to create a new TCP connection (the use of TCP connection can be optimized through the keep-alive attribute, and multiple HTTP requests can share this TCP connection). The MQTT protocol is a long connection protocol where each client maintains a long connection. Advantages over the HTTP protocol are:  

- The long connection of MQTT can be used to realize not only the message transmission from the device side to the server side, but also the real-time control message transmission from the server side to the device side. However, HTTP protocol can only realize this function through polling, which is relatively inefficient.
- The MQTT protocol sends heartbeat packets when the connection is maintained. Therefore, the protocol has built-in support for device "snap" function with the minimum cost, while the HTTP protocol needs to issue HTTP requests separately to achieve this function, and the cost of implementation is higher;
- Low bandwidth and low power consumption. MQTT has a huge advantage over HTTP in the size of the transmitted message. After the connection is established, the MQTT protocol avoids the extra resource consumption required to establish the connection. Therefore, when the actual data is sent, there is a big advantage in terms of bandwidth required for the message transmission compared with HTTP. With reference to online evaluation, MQTT transmits data nearly 50 times less than HTTP in quantity when sending the same size data, and the speed is nearly 20 times faster. Another evaluation made by someone on the internet shows that the power consumption of the MQTT protocol is only one percent of the HTTP protocol in the scenario of receiving messages, and one-tenth when sending messages.
- MQTT provides message quality control (QoS). The higher the message quality level is, the better the quality of message delivery will be. In the application scenario of the Internet of Things, users can set different message quality levels according to different usage scenarios. 



## *What's EMQ X authentication and it's use scenario？*

**Tag:** [*authentication*](tags.md#认证鉴权)

When a client connects to EMQ X server, EMQ X can authenticate it in different ways. It includes following 3 approaches, 

- Username and password: Per every MQTT client connection, which can be configured at the server, only by passing with correct username and password, the client connection can be established.
- ClientID: Every MQTT client will have a unique ClientID, and a list of ClientIds can be configured in server, and only ClientIds in the list can be authenticated successfully.
- Anonymous: Allows anonymous access.

Besides using the configuration file (to configure authentication), EMQ X can also use database and integration with external applications, such as MySQL, PostgreSQL, Redis, MongoDB, HTTP and LDAP. 




### What's Hook? What's the use scenario?

**Tag:** [*WebHook*](tags.md#webhook)


Hook is an interface provided by EMQ X, which will be triggered when a connection, session or message is established/delivered. EMQ X provides hooks listed in below, which allows user to save these triggered events to database, and user can conveniently query all kinds of information, such as client connect, disconnect. 

- client.connected: client online
- client.disconnected: client offline
- client.subscribe: client subscribes topics
- client.unsubscribe: client unsubscribes topics
- session.created: session was created
- session.resumed: session is resumed
- session.subscribed: after session subscribe topic
- session.unsubscribed: after session unsubscribe topic
- session.terminated: session is terminated
- message.publish: MQTT message is publish
- message.delivered: MQTT message is delivered
- message.acked: MQTT message is acknowledged
- message.dropped: MQTT message is dropped



## *What's mqueue? How to use mqueue in EMQ X?*

**Tag:** [*message queue*](tags.md#消息队列)

mqueue is message queue stored in session during the message publish process. If the clean session is set false in mqtt connect packet, then EMQ X would maintain session for the client which conneced to EMQ X even the client has been disconnected from EMQ X. Then the session would receive messages from the subscribed topic and store these messages into mqueue. And when the client online again, these messages would be delivered to client instantly. Because of low priority of qos 0 message in mqtt protocol, EMQ X do not cache qos 0 message into mqueue. However, the mqueue could be configured in `emqx.conf`, if this entry has been configured as `zone.$name.mqueue_store_qos0 = true`, the qos0 mesage would been stored into mqueue, too. mqueue has limit size, it would be configured with `zone.external.max_mqueue_len` to determine the number of messages cached into mqueue. Notice that these messages are stored in memory, so please do not set the mqueue length to 0 (0 means there is no limit for mqueue), otherwise it would risk running out of memory. 




### What's WebSocket? When to use Websocket to connect EMQ X?

**Tag:** [*WebSocket*](tags.md#websocket)  [*Multi-Protocol*](tags.md#多协议)

WebSocket is a full-duplex communication protocol based on HTTP protocol, user can realize dual direction communications between browser and server. Through Websocket, server can push message to web browser. EMQ X provides support of WebSocket, user can realize pub to topics and sub to topic from browsers. 




### What's shared subscription, and it's use scenario?

**Tag:** [*shared subscription*](tags.md#共享订阅)

Shared subscription is a new feature of MQTT 5.0 specification. Before the feature was introduced in MQTT 5.0 specification, EMQ 2.x already supported the feature as non-standard MQTT protocol. In general, all of subscribers will receive ALL message for the subscribed topics, while clients that subscribe the same topic will receive the message with round-robin way, so one message will not be delivered to different clients. By this way, the subscribers can be load-balanced.

Shared subscription is very useful in data collection and centralized data analysis applications. In such cases, number of data producers is much larger than consumers, and one message is ONLY need to be consumed by once.


For more usage methods, please refer to [Share Subscription](https://docs.emqx.io/tutorial/v3/cn/advanced/share_subscribe.html).




### What is an offline message?

**Tag:** [*offline message*](tags.md#离线消息)

Under normal circumstances, the MQTT client will only receive messages when it is connected to the message server. However, if the client has a fixed ClientID, clean_session is false, and the QoS setting meets the server-side configuration requirements, when the client is offline, the server can maintain a certain amount of offline messages for the client, and send it to the client when it connects again.

Offline messages are very useful when the network connection is not very stable, or there are certain requirements for QoS.



### What is proxy subscriptions？What is the use scenario？

**Tag:** [*proxy subscription*](tags.md#代理订阅)

Usually clients need to actively subscribe to topics after connecting to EMQ X. Proxy subscription means that the server subscribes to the client for the topic. This process does not require the client to participate. The correspondence between the client and the topic that needs to be subscribed to is stored in the server.

Using proxy subscriptions can centrally manage a large number of client subscriptions, while omitting the subscription step for clients, which can save computing resources and network bandwidth on the client side.

The Redis database is taken as an example. For how to use proxy subscription on EMQ X,  please refer to [Redis Implement Client Proxy Subscription](https://docs.emqx.io/tutorial/v3/cn/backend/redis.html#%E5%AE%A2%E6%88%B7%E7%AB%AF%E4%BB%A3%E7%90%86%E8%AE%A2%E9%98%85).

{% hint style="info" %}
Note: Currently EMQ X Enterprise Version supports proxy subscription.
{% endhint %}




### What is the usage of system topics? What system topics are available?

**Tag:** [*System topic*](tags.md#系统主题)

The system topics have a prefix of `$SYS/`. Periodically, EMQ X publishes system messages to system topics, these messages include system status, statistics of MQTT, client's online/offline status and so on.

Here are some examples of system topics, for a complete list of system topic please refer to EMQ X document:

- $SYS/brokers: List of nodes in cluster
- $SYS/brokers/${node}/clients/${clientid}/connected: this message is published when a client connects
- $SYS/broker/${node}/stats/connections/count: Number of connections on a node
- $SYS/broker/${node}/stats/sessions/count: Number of sessions on a node