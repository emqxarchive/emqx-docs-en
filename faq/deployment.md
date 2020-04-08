---
# 标题
title: 安装部署
# 编写日期
date: 2020-02-20 12:44:32
# 作者 Github 名称
author: wivwiv
# 关键字
keywords:
# 描述
description:
# 分类
category:
# 引用
ref:
---

# Installation and deployment
### What's EMQ X suggested OS?

EMQ X supports deployment on at Linux, Windows, MacOS, ARM system, and suggest to deploy product environment at issued Linux version, such as CentOS, Ubuntu and Debian. 




### Does EMQ X support Windows operating system？


Yes, it does. Refer to [article](https://www.jianshu.com/p/e5cf0c1fd55c) for deployment.




### How to estimate resource usage of EMQ X？

**Tag:** [*Resource estimate*](tags.md#资源估算)


Following factors will have an impact on EMQ X resource consumption, mainly on CPU and memory usage. 

- Connection number: EMQ X creates 2 Erlang process for every MQTT connection, and every Erlang process consumes some resource. The more connections, the more resource is required.
- Average throughput: Throughput means (pub message number + sub message number) processed by EMQ X per second. With higher throughput value, more resource will be used for handling route and message delivery in EMQ X.
- Payload size: With bigger size of payload, more memory and CPU are required for message cache and processing.
- Topic number: With more topics, the route table in EMQ X will increase, and more resource is required.
- QoS：With higher message QoS level, more resource will be used for message handling.

 If client devices connect to EMQ X through TLS, more CPU resource is required for encryption and decryption. Our suggested solution is to add a load balancer before EMQ X nodes, the TLS is offloaded at load balance node, connections between load balancer and backend EMQ X nodes use plain TCP connections. 

 You can use our online calculation tool [TODO](https://www.emqx.io/) to estimate the resource consumption. 




### What is the scenario of EMQ X's million connection stress test?

**Tag:** [*Performance Test*](tags.md#性能测试)

When the EMQ 2.0 version was released, a million-level connection performance test was done by the third-party software testing tool service provider [XMeter](https://www.xmeter.net). This test is based on the most popular performance testing tool [Apache JMeter](https://jmeter.apache.org/) in the open source community, and open source [performance testing plugin](https://github.com/emqx/mqtt-jmeter). Under the performance test scenario, the MQTT protocol connection from the client to the server is tested. In this test scenario, except for sending the control packet and PING packet of the MQTT protocol (every 5 minutes), user data is not sent, and the number of new connections per second is 1000, running for a total of 30 minutes.

In this test, some other performance tests were also performed, mainly including the scenario of sending and receiving messages under different conditions in the case of a 100,000 MQTT connection. For details, please refer to  [Performance Test Report](https://media.readthedocs.org/pdf/emq-xmeter-benchmark-cn/latest/emq-xmeter-benchmark-cn.pdf).




### If the number of my connections is not large, does EMQ X production environment deployment require multiple nodes？

**Tag:** [*cluster*](tags.md#集群)

Even if the number of connections and the message rate are not high (low load for the server), it still makes sense to deploy a multi-node cluster in a production environment. Cluster can improve system availability and reduce the possibility of single points of failure. When a node goes down, other online nodes can ensure the service of the entire system is not interrupted.