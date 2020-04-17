---
# 标题
title: Features List
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
ref: undefined
---

# Features List

- Full MQTT V3.1 / V3.1.1 and V5.0 protocol specification support
  - QoS0, QoS1, QoS2 message support
  - Persistent conversation and offline message support
  - Retained message support
  - Last Will message support
- TCP / SSL connection support
- MQTT / WebSocket / SSL support
- HTTP message publishing interface support
- $SYS/\# system theme support
- Client online status query and subscription support
- Client ID or IP address authentication support
- User name and password authentication support
- LDAP authentication
- Redis, MySQL, PostgreSQL, MongoDB, HTTP authentication integration
- Browser cookie authentication
- Access control (ACL) based on client ID, IP address, user name
- Multi-server node cluster (Cluster)
- Support manual, mcast, dns, etcd, k8s and other cluster discovery methods
- Automatic network partition healing
- Message rate limit
- Connection rate limit
- Configure nodes by partition
- Multi-server node bridge (Bridge)
- MQTT Broker bridge support
- Stomp protocol support
- MQTT-SN protocol support
- CoAP protocol support
- Stomp/SockJS support
- Delay Publish ($delay/topic)
- Flapping detection
- Blacklist support
- Shared subscription($share/\<group\>/topic)
- TLS / PSK support
- 规则引擎
  - 空动作 (调试)
  - 消息重新发布
  - 桥接数据到 MQTT Broker
  - 检查 (调试)
  - 发送数据到 Web 服务

<strong class="emqxce">
以下是 EMQ X Enterprise 特有功能
</strong>

- Scalable RPC 架构: 分离 Erlang 自身的集群通道与 EMQ X 节点间的数据通道
- 数据持久化
  - Redis 存储订阅关系、设备在线状态、MQTT 消息、保留消息，发布 SUB/UNSUB 事件
  - MySQL 存储订阅关系、设备在线状态、MQTT 消息、保留消息
  - PostgreSQL 存储订阅关系、设备在线状态、MQTT 消息、保留消息
  - MongoDB 存储订阅关系、设备在线状态、MQTT 消息、保留消息
  - Cassandra 存储订阅关系、设备在线状态、MQTT 消息、保留消息
  - DynamoDB 存储订阅关系、设备在线状态、MQTT 消息、保留消息
  - InfluxDB 存储 MQTT 时序消息
  - OpenTDSB 存储 MQTT 时序消息
  - TimescaleDB 存储 MQTT 时序消息
- 消息桥接
  - Kafka 桥接：EMQ X 内置 Bridge 直接转发 MQTT 消息、设备上下线事件到 Kafka
  - RabbitMQ 桥接：EMQ X 内置 Bridge 直接转发 MQTT 消息、设备上下线事件到 RabbitMQ
  - Pulsar 桥接：EMQ X 内置 Bridge 直接转发 MQTT 消息、设备上下线事件到 Pulsar
  - RocketMQ 桥接：EMQ X 内置 Bridge 直接转发 MQTT 消息、设备上下线事件到 RocketMQ
- 规则引擎
  - 消息编解码
  - 桥接数据到 Kafka
  - 桥接数据到 RabbitMQ
  - 桥接数据到 RocketMQ
  - 桥接数据到 Pulsar
  - 保存数据到 PostgreSQL
  - 保存数据到 MySQL
  - 保存数据到 OpenTSDB
  - 保存数据到 Redis
  - 保存数据到 DynamoDB
  - 保存数据到 MongoDB
  - 保存数据到 InfluxDB
  - 保存数据到 Timescale
  - 保存数据到 Cassandra
- Schema Registry：将 EMQ X 的事件、消息 提供了数据编解码能力

{% emqxce %}

[EMQ X Enterprise](https://www.emqx.io/cn/products/enterprise) is a powerful enterprise-level IoT MQTT messaging platform built by the people who develop the open source EMQ X Broker.

EMQ X Enterprise supports one-stop access to millions of IoT devices, MQTT&CoAP multi-protocol processing, and low-latency real-time communication. It maintains the simplicity and high performance of EMQ X Broker, while adding many enterprise-level features:

- The connection performance is enhanced to millions or tens of millions. It supports private protocol and industry protocol customization, is compatible with old network equipment access based on TCP / UDP private protocol, and supports full network multi-protocol equipment access;
- It supports Redis, MySQL, PostgreSQL, MongoDB and other database message data persistence, with message conversion and written to multiple time series databases of InfluxDB, OpenTSDB, TimescaleDB. It supports automatic loading subscriptions from Redis or database, without the need for the initiation from client;
- Message bridging of message and stream middleware: forwarding of messages to Kafka stream processing middleware with 100,000/s High-performance and highly reliable , seamless integration with enterprise message middleware of RabbitMQ, Pulsar ;
- With global professional team technical support. Our team covers 5 branches in North America, Europe, and China. There are professional founding teams from Huawei, IBM, Amazon, and nearly ten partners in Europe, North America, and India, providing First-class technical support and consulting services.

{% hint style="info" %}
Thank you for your support of EMQ X Broker. If you need business services, please contact our sales staff: sales-cn@emqx.io。
{% endhint %}

{% endemqxce %}

{% emqxee %}
## 功能说明
{% endemqxee %}


{% emqxce %}
## 企业版功能
{% endemqxce %}

### 消息数据存储

EMQ X 企业版支持存储订阅关系、MQTT 消息、设备状态到
Redis、MySQL、PostgreSQL、MongoDB、Cassandra、TimescaleDB、InfluxDB、DynamoDB、OpenTDSB
数据库:

![image](assets/overview_4.png)

数据存储相关配置，详见"数据存储"章节。

### 消息桥接转发

EMQ X 企业版支持直接转发 MQTT 消息到 RabbitMQ、Kafka、Pulsar、RocketMQ、MQTT
Broker，可作为百万级的物联网接入服务器(IoT Hub):

![image](assets/overview_5.png)

### 规则引擎

EMQ X 规则引擎可以灵活地处理消息和事件。

EMQ X 企业版规则引擎支持消息重新发布；桥接数据到
Kafka、Pulsar、RocketMQ、RabbitMQ、MQTT Broker；保存数据到
MySQL、PostgreSQL、Redis、MongoDB、DynamoDB、Cassandra、InfluxDB、OpenTSDB、TimescaleDB；发送数据到
WebServer:

![image](assets/overview_6.png)

规则引擎相关配置，详见"规则引擎"章节。

### 编解码

Schema Registry
目前可支持三种格式的编解码：[Avro](https://avro.apache.org)，[Protobuf](https://developers.google.com/protocol-buffers/)，以及自定义编码。其中
Avro 和 Protobuf 是依赖 Schema 的数据格式，编码后的数据为二进制，解码后为 Map 格式
。解码后的数据可直接被规则引擎和其他插件使用。用户自定义的
(3rd-party) 编解码服务通过 HTTP 或 TCP 回调的方式，进行更加贴近业务需求的编解码。

![image](assets/overview_7.png)

编解码相关配置，详见"编解码"章节。


{% emqxce %}
## EMQ X 不同版本对比

![EMQ X 开源版、企业版和专业版的对比](assets/3441587031341_.pic_hd.jpg)

{% endemqxce %}
