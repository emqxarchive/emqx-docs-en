---
# 标题
title: 使用教程
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

# User Guide

### How to use EMQ X?

 EMQ X Broker is free and it can be download at [https://www.emqx.io/downloads#broker](https://www.emqx.io/downloads#enterprise#broker). 

 EMQ X Enterprise can be downloaded and evaluated for free. You can download it from https://www.emqx.io/downloads#enterprise, and then apply trial license at https://www.emqx.io/licenses#trial. 

 Also you can use the EMQ X enterprise version through public cloud service. 

- [Aliyun](https://market.aliyun.com/products/56014009/cmjj029979.html?spm=5176.730005.productlist.d_cmjj029979.69013524xism4L&innerSource=search_EMQ)

- [Qingcloud](https://appcenter.qingcloud.com/search/category/iot)



### How to update EMQ X license?

**Tag:** [*License*](tags.md#license)

 After clicking "Download License", browse to the "license.zip" file that you downloaded. 

 Copy the two files(emqx.lic, emqx.key) in the zip file to the EMQX license directory. 

- If your installation package is a zip file, the licenses are under "emqx/etc/";
- For DEB/RPM package, the licenses are under "/etc/emqx/";
- For Docker image, the licenses are under "/opt/emqx/etc/".

 After the copy is completed, the license needs to be reloaded from the command line to complete the update： 

Basic commands:

```
emqx_ctl license reload [license file path]
```

 The update commands for different installation modes 

```
## zip packages
./bin/emqx_ctl license reload etc/emqx.lic

## DEB/RPM
emqx_ctl license reload /etc/emqx/emqx.lic

## Docker
docker exec -it emqx-ee emqx_ctl license reload /opt/emqx/etc/emqx.lic
```



### Can EMQ X support customized protocols? How to implement?

**Tag:** [*Protocol*](tags.md#多协议)  [*Extend*](tags.md#扩展)

For newly developed private protocols, EMQ X provides a set of TCP protocol access specifications, and the private protocol can be developed and accessed in accordance with the specifications. If the protocol you are using has been finalized or the bottom layer of the protocol is not TCP, it can be converted through the gateway, and then access EMQ X through the MQTT protocol, or you can directly contact EMQ to officially support private protocol adaptation.



### Can I capture device online and offline events? How to use it？

**Tag:** [*WebHook*](tags.md#webhook)  [*System topic*](tags.md#系统主题)

EMQ X supports to capture device online and offline events through below 3 approaches,

- Web Hook

- Subscribe related $SYS topics

  - $SYS/brokers/${node}/clients/${clientid}/connected
  - $SYS/brokers/${node}/clients/${clientid}/disconnected

- Directly save events into database

  The final approach is only supported in enterprise version, and supported database includes Redis, MySQL, PostgreSQL, MongoDB and Cassandra. User can configure database, client.connected and client.disconnected events in the configuration file. When a device is online or offline, the information will be saved into database.




### I want to control topics can be used for specific clients, how to configure it in EMQ X?

**Tag:** [*ACL*](tags.md#acl)  [*Publish/Subscribe*](tags.md#发布订阅)

EMQ X can constrain clients used topics to realize device access controls. To use this feature, ACL (Access Control List) should be enabled, disable anonymous access and set `acl_nomatch` to 'deny' (For the convenience of debugging, the last 2 options are enabled by default, and please close them). 

```bash
## etc/emqx.conf

## ACL nomatch
mqtt.acl_nomatch = allow
```

 ACL can be configured in configuration file, or backend databases. Below is one of sample line for ACL control file, the meaning is user 'dashboard' can subscribe '$SYS/#' topic. ACL configuration in backend databases is similar, refer to EMQ X document [ACL access control](https://docs.emqx.io/tutorial/v3/cn/security/acl.html) for more detailed configurations. 

```
{allow, {user, "dashboard"}, subscribe, ["$SYS/#"]}.
```



### Can EMQ X support traffic control？

**Tag:** [*traffic control*](tags.md#流量控制)


Yes. Currently EMQ X supports to control connection rate and message publish rate. Refer to below for sample configuration. 

```
## Value: Number
listener.tcp.external.max_conn_rate = 1000

## Value: rate,burst
listener.tcp.external.rate_limit = 1024,4096
```




### How does the EMQ X achieve high concurrency and high availability?

**Tag:** [*performance*](tags.md#性能)  [*high concurrency*](tags.md#高并发)

High concurrency and availability are design goals of EMQ X. To achieve these goals, several technologies are applied:

- Making maximum use of the soft-realtime, high concurrent and fault-tolerant Erlang/OTP platform;
- Full asynchronous architecture;
- Layered design of connection, session, route and cluster;
- Separated messaging and control panel;

With the well design and implementation, a single EMQ X cluster can handle million level connections.

EMQ X supports clustering. The EMQ X performance can be scale-out with the increased number of nodes in cluster, and the MQTT service will not be interrupted when a single node is down.



### Can EMQ X store messages to database？

**Tag:** [*persistence*](tags.md#持久化)

EMQ X Enterprise Edition supports message persistence, and you can save messages to the database. The open source version does not yet supporte. The current databases supported by EMQ X Enterprise Edition message persistence are:

- Redis
- MongoDB
- MySQL
- PostgreSQL
- Cassandra
- AWS DynamoDB
- TimescaleDB
- OpenTSDB
- InfluxDB

For support of data persistence, please refer to [EMQ X Data Persistence Overview](https://docs.emqx.io/tutorial/v3/cn/backend/whats_backend.html).



### Can I disconnect an MQTT connection from EMQ X server？

**Tag:** [*HTTP API*](tags.md#http-api)  [*Dashboard*](tags.md#dashboard)

Yes. You can do it by invoking REST API provied by EMQ X, but the implementation is different in EMQ X 2.x and 3.x:

- EMQ X customized protocol in 2.x versions.
- Follow the process defined in MQTT 5.0 protocol after version 3.0.

Refer to below for API invocation:

```html
HTTP 方法：DELETE 
URL：api/[v2|v3]/clients/{clientid} 
<!--请注意区分 URL 中第二部分的版本号，请根据使用的版本号来决定 -->

返回内容：
{
    "code": 0,
    "result": []
}
```

For HTTP API usage method, refer to [HTTP API](https://docs.emqx.io/broker/v3/cn/rest.html)



### Can EMQ X forward messages to Kafka？

**Tag:** [*Kafka*](tags.md#kafka)  [*bridge*](tags.md#桥接)  [*persistence*](tags.md#持久化)

Yes. The EMQ X Enterprise edition integrates a Kafka bridge, it can bridge data to Kafka. 

For Kafka usage,  refer to [EMQ X bridge data to Kafka](https://docs.emqx.io/tutorial/v3/cn/bridge/bridge_to_kafka.html)


### I use Kafka bridge in EMQ X enterprise, when will the MQTT Ack packet sent back to client? Is the time when message arriving EMQ X or after getting Ack message from Kafka?

**Tag:** [*Kafka*](tags.md#kafka)  [*Configuration*](tags.md#配置)


 It's up to Kafka bridge configuration, the configuration file is at  `/etc/emqx/plugins/emqx_bridge_kafka.conf`

```bash
## Pick a partition producer and sync/async.
bridge.kafka.produce = sync
```

- Sync: MQTT Ack packet will be sent back to client after receiving Ack from Kafka.
- Async: MQTT Ack packet will be sent back to client right after EMQ X receiving the message, and EMQ X will not wait the Ack returned from Kafka.

If the backend Kafka server is not available, then the message will be accumulated in EMQ X broker.

- The message will be cached in memory before EMQ X 2.4.3 version, if the memory is exhausted, then the EMQ X server will be down.
- The message will be cached in disk after EMQ X 2.4.3 version, message will probably lost if the disk is full.

So we suggest you to closely monitor Kafka server, and recover Kafka service as soon as possible when it has any questions.




### EDoes EMQ X support cluster auto discovery? What clustering methods are supported？

**Tag:** [*cluster*](tags.md#集群)

EMQ X supports cluster auto discovery. EMQ X clustering can be done manually or automatically.

Currently supported clustering methods:

- Manual clustering
- Static clustering
- Auto clustering using IP multi-cast
- Auto clustering using DNS
- Auto clustering using ETCD
- Auto clustering using K8S

Please refer to  [EMQ X 's cluster concept](https://docs.emqx.io/tutorial/v3/cn/cluster/whats_cluster.html) for the cluster concept and how to form a cluster



### Can I forward MQTT messages EMQ X to other MQTT broker, like RabbitMQ？

**Tag:** [*RabbitMQ*](tags.md#rabbitmq)  [*bridge*](tags.md#桥接)  [*persistence*](tags.md#持久化)


EMQ X support forward messages to other MQTT broker. Using MQTT bridge, EMQ X can forward messages of interested topics to other broker. 

For EMQ X bridging related usage method, please refer to [EMQ X bridge](https://docs.emqx.io/tutorial/v3/cn/bridge/bridge.html).




### Can I forward messages from EMQ X to MQTT services hosted on public cloud? Such as AWS or Azure's IoT Hub？

**Tag:** [*bridge*](tags.md#桥接)

EMQ X can forward messages to standard MQTT Broker, including IoT Hub hosted on public cloud, which is a feature of EMQ X bridge. 



### Can other MQTT broker (for example Mosquitto) forward messages to EMQ X?

**标签:** [*Mosquitto*](tags.md#mosquitto)  [*bridge*](tags.md#桥接)

Mosquitto can forward messages to EMQ X, please refer to [data bridge](../advanced/bridge.md)

{% hint style="info" %}
> For EMQ X bridging related usage method, please refer to [EMQ X bridge](https://docs.emqx.io/tutorial/v3/cn/bridge/bridge.html).
{% endhint %}


### What should I do if I want trace the subscription and publish of some particular message?

**Tag:** [*Trace*](tags.md#trace)  [*Debug*](tags.md#调试)


 EMQ X support the tracing of messages from particular client or under particular topic. You can use the command line tool `emqx_ctl` for tracing. The example below shows how to trace messages under 'topic' and save the result in 'trace_topic.log'. For more details, please refer to EMQ X document. 

```
./bin/emqx_ctl trace topic "topic" "trace_topic.log"
```




### Why does the number of connections and throughput always fail to increase when I do a stress test, is there a system tuning guide?

**Tag:** [*Debug*](tags.md#调试)  [*Performance Test*](tags.md#性能测试)

When performing stress test, in addition to selecting hardware with sufficient computing power, we also need to make certain adjustments to the software operating environment. For example, we can modify the operating system's global maximum number of file handles, the allowed number of file handles for the user to open, TCP backlog and buffer, Erlang virtual machine process limit, etc. We can also make some tuning on the client to ensure that the client can have sufficient connection resources.

There are different ways to tune the system under different requirements. There are more detailed instructions for tuning in common scenarios in EMQ X's [Document-Test Tuning](https://developer.emqx.io/docs/broker/v3/cn/tune.html)


### Does EMQ X support encrypted connection? What is the recommended deployment?

**Tag:** [*TLS*](tags.md#tls)  [*Encrypted connection*](tags.md#加密连接)


 EMQ X Support SSL/TLS. In production, we recommend to terminate the TLS connection by Load Balancer. By this way, the connection between device and server(load balancer) use secured connection, and connection between load balancer and EMQ X nodes use general TCP connection. 



### How to troubleshoot if EMQ X can't start after installation？

**Tag:** [*Debug*](tags.md#调试)


 Execute `$ emqx console` to view the output. 

+	`logger`  command is missing 

  ```
  $ emqx console
  Exec: /usr/lib/emqx/erts-10.3.5.1/bin/erlexec -boot /usr/lib/emqx/releases/v3.2.1/emqx -mode embedded -boot_var ERTS_LIB_DIR /usr/lib/emqx/erts-10.3.5.1/../lib -mnesia dir "/var/lib/emqx/mnesia/emqx@127.0.0.1" -config /var/lib/emqx/configs/app.2019.07.23.03.07.32.config -args_file /var/lib/emqx/configs/vm.2019.07.23.03.07.32.args -vm_args /var/lib/emqx/configs/vm.2019.07.23.03.07.32.args -- console
  Root: /usr/lib/emqx
  /usr/lib/emqx
  /usr/bin/emqx: line 510: logger: command not found
  ```
  
   **Solution:** 
  
  + `Centos/Redhat`
  
    ```
    $ yum install rsyslog
    ```
  
  + `Ubuntu/Debian`
  
    ```
    $ apt-get install bsdutils
    ```
  
+	`openssl`  is missing 

```
    $ emqx console
    Exec: /emqx/erts-10.3/bin/erlexec -boot /emqx/releases/v3.2.1/emqx -mode embedded -boot_var ERTS_LIB_DIR /emqx/erts-10.3/../lib -mnesia dir "/emqx/data/mnesia/emqx@127.0.0.1" -config /emqx/data/configs/app.2019.07.23.03.34.43.config -args_file /emqx/data/configs/vm.2019.07.23.03.34.43.args -vm_args /emqx/data/configs/vm.2019.07.23.03.34.43.args -- console
    Root: /emqx
    /emqx
    Erlang/OTP 21 [erts-10.3] [source] [64-bit] [smp:8:8] [ds:8:8:10] [async-threads:32] [hipe]
    
    {"Kernel pid terminated",application_controller,"{application_start_failure,kernel,{{shutdown,{failed_to_start_child,kernel_safe_sup,{on_load_function_failed,crypto}}},{kernel,start,[normal,[]]}}}"}
    Kernel pid terminated (application_controller) ({application_start_failure,kernel,{{shutdown,{failed_to_start_child,kernel_safe_sup,{on_load_function_failed,crypto}}},{kernel,start,[normal,[]]}}})
    
    Crash dump is being written to: log/crash.dump...done
```

 **Solution:** Install openssl above version 1.1.1 

+ `License` is missing

```
  $ emqx console
  Exec: /usr/lib/emqx/erts-10.3.5.1/bin/erlexec -boot /usr/lib/emqx/releases/v3.2.1/emqx -mode embedded -boot_var ERTS_LIB_DIR /usr/lib/emqx/erts-10.3.5.1/../lib -mnesia dir "/var/lib/emqx/mnesia/emqx@127.0.0.1" -config /var/lib/emqx/configs/app.2019.07.23.05.52.46.config -args_file /var/lib/emqx/configs/vm.2019.07.23.05.52.46.args -vm_args /var/lib/emqx/configs/vm.2019.07.23.05.52.46.args -- console
  Root: /usr/lib/emqx
  /usr/lib/emqx
  Erlang/OTP 21 [erts-10.3.5.1] [source] [64-bit] [smp:8:8] [ds:8:8:10] [async-threads:32] [hipe]
  
  Starting emqx on node emqx@127.0.0.1
  Start http:management listener on 8080 successfully.
  Start http:dashboard listener on 18083 successfully.
  Start mqtt:tcp listener on 127.0.0.1:11883 successfully.
  Start mqtt:tcp listener on 0.0.0.0:1883 successfully.
  Start mqtt:ws listener on 0.0.0.0:8083 successfully.
  Start mqtt:ssl listener on 0.0.0.0:8883 successfully.
  Start mqtt:wss listener on 0.0.0.0:8084 successfully.
  EMQ X Broker 3.2.1 is running now!
  "The license certificate is expired!"
  2019-07-23 05:52:51.355 [critical] The license certificate is expired!
  2019-07-23 05:52:51.355 [critical] The license certificate is expired! System shutdown!
  Stop mqtt:tcp listener on 127.0.0.1:11883 successfully.
  Stop mqtt:tcp listener on 0.0.0.0:1883 successfully.
  Stop mqtt:ws listener on 0.0.0.0:8083 successfully.
  Stop mqtt:ssl listener on 0.0.0.0:8883 successfully.
  Stop mqtt:wss listener on 0.0.0.0:8084 successfully.
  [os_mon] memory supervisor port (memsup): Erlang has closed
  [os_mon] cpu supervisor port (cpu_sup): Erlang has closed
```

 **Solution:** Go to [emqx.io](https://emqx.io/) to apply for a license or install the open source version of EMQ X Broker 



### Use of ssl resumption session in EMQ X

**Tag:** [*TLS*](tags.md#tls)

Modify the reuse_sessions=on in the emqx.conf configuration and take effect. If the client and the server are successfully connected through SSL, when the client connection is encountered for the second time, the SSL handshake phase is skipped, the connection is directly established to save the connection time and increase the client connection speed. 



### MQTT client disconnect statistics

**Tag:** [*Metrics*](tags.md#指标)

Execute `emqx_ctl listeners` to view the `shutdown_count` statistics under the corresponding port.

Client disconnect link error code list:

- `keepalive_timeout`: MQTT keepalive time out
- `closed`:  TCP client disconnected (the FIN sent by the client did not receive the MQTT DISCONNECT)
- `normal`:  MQTT client is normally disconnected
- `einval`: EMQ X wants to send a message to the client, but the Socket has been disconnected
- `function_clause`: MQTT packet format error
- `etimedout`: TCP Send timeout (no TCP ACK response received)
- `proto_unexpected_c`: Repeatedly received an MQTT connection request when there is already an MQTT connection
- `idle_timeout`:After the TCP connection is established for 15s, the connect packet has not been received yet.