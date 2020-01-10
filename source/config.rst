
.. _configuration:

=============
Configuration
=============

The main configuration files of the *EMQ X* broker are under 'etc/' folder:

+----------------------+-----------------------------------+
| File                 | Description                       |
+----------------------+-----------------------------------+
| etc/emqx.conf        | *EMQ X* Configuration File        |
+----------------------+-----------------------------------+
| etc/acl.conf         | The default ACL File              |
+----------------------+-----------------------------------+
| etc/plugins/\*.conf  | Config Files of Plugins           |
+----------------------+-----------------------------------+

----------------------------------
EMQ X Configuration Change History
----------------------------------

The EMQ X configuration file has been adjusted four times for the convenience of users and plug-in developers.

1. The EMQ X 1.x version uses the Erlang native configuration file format etc/emqttd.config:

.. code-block:: erlang

    {emqttd, [
      %% Authentication and Authorization
      {access, [
        %% Authetication. Anonymous Default
        {auth, [
            %% Authentication with username, password
            %{username, []},

            %% Authentication with clientid
            %{clientid, [{password, no}, {file, "etc/clients.config"}]},

Erlang's native configuration format is multi-level nested, which is very unfriendly to non-Erlang developer users.

2. EMQ X 2.0-beta.x version simplifies the native Erlang configuration file in a format similar to rebar.config or relx.config:

.. code-block:: erlang

    %% Max ClientId Length Allowed.
    {mqtt_max_clientid_len, 512}.

    %% Max Packet Size Allowed, 64K by default.
    {mqtt_max_packet_size, 65536}.

    %% Client Idle Timeout.
    {mqtt_client_idle_timeout, 30}. % Second

The simplified Erlang native configuration format is user-friendly, but plug-in developers have to rely on the gen_conf library instead of appliaton: get_env to read configuration parameters.

3. The EMQ X 2.0-rc.2 integrates the cuttlefish library, adopts the k = V universal format similar to sysctl, and translates it into Erlang native configuration format at system startup:

.. code-block:: properties

    ## Node name
    node.name = emq@127.0.0.1

    ## Max ClientId Length Allowed.
    mqtt.max_clientid_len = 1024

4. From EMQ X 3.0-beta.1 beta version, the emqttd is renamed to emqx, and configuration names and configuration values/information are changed in corresponding:

.. code-block:: properties

    ## Profile
    etc/emq.config  ==> etc/emqx.config

    ## Node name
    Original:
    node.name = emq@127.0.0.1
    Now:
    node.name = emqx@127.0.0.1


 Configuration file processing flow during EMQ X start-up::

    ----------------------                                          3.0/schema/*.schema      -------------------
    | etc/emqx.conf      |                   -----------------              \|/              | data/app.config |
    |       +            | --> mergeconf --> | data/app.conf | -->  cuttlefish generate  --> |                 |
    | etc/plugins/*.conf |                   -----------------                               | data/vm.args    |
    ----------------------                                                                   -------------------

------------------------
OS Environment Variables
------------------------

+------------------+----------------------------------------+
| EMQX_NODE_NAME   | Erlang node name                       |
+------------------+----------------------------------------+
| EMQX_NODE_COOKIE | Cookie for distributed erlang node     |
+------------------+----------------------------------------+
| EMQX_MAX_PORTS   | Maximum number of opened sockets       |
+------------------+----------------------------------------+
| EMQX_TCP_PORT    | MQTT TCP Listener Port, Default: 1883  |
+------------------+----------------------------------------+
| EMQX_SSL_PORT    | MQTT SSL Listener Port, Default: 8883  |
+------------------+----------------------------------------+
| EMQX_WS_PORT     | MQTT/WebSocket Port, Default: 8083     |
+------------------+----------------------------------------+
| EMQX_WSS_PORT    | MQTT/WebSocket/SSL Port, Default: 8084 |
+------------------+----------------------------------------+

-------------------
EMQ X Cluster Setup
-------------------

Cluster name:

.. code-block:: properties

    cluster.name = emqxcl

Specify the Erlang distributed protocol:

.. code-block:: properties

    cluster.proto_dist = inet_tcp

Cluster discovery strategy:

.. code-block:: properties

    cluster.discovery = manual

Cluster Autoheal:

.. code-block:: properties

    cluster.autoheal = on

Cluster Autoclean:

.. code-block:: properties

    cluster.autoclean = 5m

EMQ X Autodiscovery Strategy
----------------------------

*EMQ X* supports node discovery and autocluster with various strategies:

+------------+---------------------------------+
| Strategy   | Description                     |
+============+=================================+
| manual     | Create cluster manually         |
+------------+---------------------------------+
| static     | Autocluster by static node list |
+------------+---------------------------------+
| mcast      | Autocluster by UDP Multicast    |
+------------+---------------------------------+
| dns        | Autocluster by DNS A Record     |
+------------+---------------------------------+
| etcd       | Autocluster using etcd          |
+------------+---------------------------------+
| k8s        | Autocluster on Kubernetes       |
+------------+---------------------------------+

**Create cluster manually**

This is the default configuration of clustering, nodes join a cluster by executing `./bin/emqx_ctl join <Node>` CLI command:

.. code-block:: properties

    cluster.discovery = manual

**Autocluster by static node list**

Configuration of static cluster discovery:

.. code-block:: properties

    cluster.discovery = static

Static node list:

.. code-block:: properties

    cluster.static.seeds = emqx1@127.0.0.1,emqx2@127.0.0.1

**Autocluster by IP Multicast**

Configuration of cluster discovery by IP multicast:

.. code-block:: properties

    cluster.discovery = mcast

IP multicast address:

.. code-block:: properties

    cluster.mcast.addr = 239.192.0.1

Multicast port range:

.. code-block:: properties

    cluster.mcast.ports = 4369,4370

Network adapter address:

.. code-block:: properties

    cluster.mcast.iface = 0.0.0.0

Multicast TTL:

.. code-block:: properties

    cluster.mcast.ttl = 255

Whether to send multicast packets cyclically:

.. code-block:: properties

    cluster.mcast.loop = on

**Autocluster by DNS A Record**

Configuration of cluster discovery by DNS A record:

.. code-block:: properties

    cluster.discovery = dns

dns name:

.. code-block:: properties

    cluster.dns.name = localhost

Application name is used to build the node name with the IP address:

.. code-block:: properties

    cluster.dns.app  = emqx

**Autocluster using etcd**

Configure cluster discovery by etcd:

.. code-block:: properties

    cluster.discovery = etcd

List of etcd servers, separated by ``,`` :

.. code-block:: properties

    cluster.etcd.server = http://127.0.0.1:2379

The prefix of the node's path in etcd. Each node in the cluster creates the following path in etcd: v2/keys/<prefix>/<cluster.name>/<node.name>:

.. code-block:: properties

    cluster.etcd.prefix = emqxcl

The TTL of the node in etcd:

.. code-block:: properties

    cluster.etcd.node_ttl = 1m

Path containing the client's private PEM encoded key file:

.. code-block:: properties

    cluster.etcd.ssl.keyfile = etc/certs/client-key.pem

Path containing the client certificate file:

.. code-block:: properties

    cluster.etcd.ssl.certfile = etc/certs/client.pem

Path containing the PEM-encoded CA certificate file:

.. code-block:: properties

    cluster.etcd.ssl.cacertfile = etc/certs/ca.pem

**Autocluster on Kubernetes**

Cluster discovery strategy is k8s:

.. code-block:: properties

    cluster.discovery = k8s

List of Kubernetes API servers, separated by ``,``:

.. code-block:: properties

    cluster.k8s.apiserver = http://10.110.111.204:8080

The service name of the EMQ X node in the cluster:

.. code-block:: properties

    cluster.k8s.service_name = emqx

Address type used to extract hosts from k8s services:

.. code-block:: properties

    cluster.k8s.address_type = ip

EMQ X node name:

.. code-block:: properties

    cluster.k8s.app_name = emqx

Kubernetes namespace:

.. code-block:: properties

    cluster.k8s.namespace = default

----------------------
EMQ X Node and Cookie
----------------------

Erlang node name:

.. code-block:: properties

    node.name = emqx@127.0.0.1

Erlang communication cookie within distributed nodes:

.. code-block:: properties

    node.cookie = emqxsecretcookie

.. NOTE::

    The Erlang/OTP platform application is composed of distributed Erlang nodes (processes). Each Erlang node (process) needs to be assigned a node name for mutual communication between nodes.
    All Erlang nodes (processes) in communication are authenticated by a shared cookie.

----------------------------
EMQ X Node Connection Method
----------------------------

The EMQ X node is based on IPv4, IPv6 or TLS protocol of Erlang/OTP platform for connections:

.. code-block:: properties

    ##  Specify the Erlang Distributed Communication Protocol: inet_tcp | inet6_tcp | inet_tls
    cluster.proto_dist = inet_tcp

    ## Specify the parameter configuration of Erlang Distributed Communication SSL
    ## node.ssl_dist_optfile = etc/ssl_dist.conf

--------------------
Erlang VM Parameters
--------------------

Erlang system heartbeat monitoring during running. Comment this line to disable heartbeat monitoring, or set the value as ``on`` to enable the function :

.. code-block:: properties

    node.heartbeat = on

The number of threads in the asynchronous thread pool, with the valid range: 0-1024:

.. code-block:: properties

    node.async_threads = 32

The maximum number of processes allowed by the Erlang VM. An MQTT connection consumes 2 Erlang processes:

.. code-block:: properties

    node.process_limit = 2048000

The maximum number of ports allowed by the Erlang VM. One MQTT connection consumes 1 Port:

.. code-block:: properties

    node.max_ports = 1024000

Allocate buffer busy limit:

.. code-block:: properties

    node.dist_buffer_size = 8MB

The maximum number of ETS tables. Note that mnesia and SSL will create a temporary ETS table:

.. code-block:: properties

    node.max_ets_tables = 256000

GC frequency:

.. code-block:: properties

    node.fullsweep_after = 1000

Crash dump log file location:

.. code-block:: properties

    node.crash_dump = log/crash.dump

Files for storing SSL/TLS options when Erlang  distributed using TLS:

.. code-block:: properties

    node.ssl_dist_optfile = etc/ssl_dist.conf

Tick time of distributed nodes:

.. code-block:: properties

    node.dist_net_ticktime = 60

Port range of TCP connections for communication between Erlang distributed nodes:

.. code-block:: properties

    node.dist_listen_min = 6396
    node.dist_listen_max = 6396

---------------------------
RPC Parameter Configuration
---------------------------

RPC Mode (sync | async):

.. code-block:: properties

    rpc.mode = async

Max batch size of async RPC requests:

.. code-block:: properties

    rpc.async_batch_size = 256

TCP port for RPC (local):

.. code-block:: properties

    rpc.tcp_server_port = 5369

TCP port for RPC(remote):

.. code-block:: properties

    rpc.tcp_client_port = 5369

Number of outgoing RPC connections.

.. code-block:: properties

    rpc.tcp_client_num = 32

RPC connection timeout:

.. code-block:: properties

    rpc.connect_timeout = 5s

RPC send timeout:

.. code-block:: properties

    rpc.send_timeout = 5s

Authentication timeout:

.. code-block:: properties

    rpc.authentication_timeout = 5s

Synchronous call timeout:

.. code-block:: properties

    rpc.call_receive_timeout = 15s

Maximum keep-alive time when socket is idle:

.. code-block:: properties

    rpc.socket_keepalive_idle = 900s

Socket keep-alive detection interval:

.. code-block:: properties

    rpc.socket_keepalive_interval = 75s

The maximum number of heartbeat detection failures before closing the connection:

.. code-block:: properties

    rpc.socket_keepalive_count = 9

Size of TCP send buffer:

.. code-block:: properties

    rpc.socket_sndbuf = 1MB

Size of TCP receive buffer:

.. code-block:: properties

    rpc.socket_recbuf = 1MB

Size of user-level software socket buffer:

.. code-block:: properties

    rpc.socket_buffer = 1MB

----------------------------
Log Parameter Configuration
----------------------------

Log output location, it can be set to write to the terminal or write to the file:

.. code-block:: properties

    log.to = both

Set the log level:

.. code-block:: properties

    log.level = warning

Set the primary logger level and the log level of all logger handlers to the file and terminal.

Set the storage path of the log file:

.. code-block:: properties

    log.dir = log

Set the file name for storing the "log.level":

.. code-block:: properties

    log.file = emqx.log

Set the maximum size of each log file:

.. code-block:: properties

    log.rotation.size = 10MB

Set the maximum number of files for log rotation:

.. code-block:: properties

    log.rotation.count = 5

The user can write a level of log to a separate file by configuring additional file logger handlers in the format log.$level.file = $filename.

For example, the following configuration writes all logs higher than or equal to the info level to the info.log file:

.. code-block:: properties

    log.info.file = info.log

--------------------------------------
Anonymous Authentication and ACL Files
--------------------------------------

Whether to allow the client to pass the authentication as an anonymous identity:

.. code-block:: properties

    allow_anonymous = true

EMQ X supports ACLs based on built-in ACLs and plugins such as MySQL and PostgreSQL.

Set whether to allow access when all ACL rules cannot match:

.. code-block:: properties

    acl_nomatch = allow

Set the default file for storing ACL rules:

.. code-block:: properties

    acl_file = etc/acl.conf

Set whether to allow ACL caching:

.. code-block:: properties

    enable_acl_cache = on

Set the maximum number of ACL caches for each client:

.. code-block:: properties

    acl_cache_max_size = 32

Set the effective time of the ACL cache:

.. code-block:: properties

    acl_cache_ttl = 1m

Etc/acl.conf access control rule definition::

    Allow|Deny User|IP Address|ClientID Publish|Subscribe Topic List

The access control rules are in the Erlang tuple format, and the access control module matches the rules one by one:

.. image:: _static/images/config_1.png

``etc/acl.conf`` default access rule settings:

Allow ``dashboard`` users to subscribe to ``$SYS/#``:

.. code-block:: erlang

    {allow, {user, "dashboard"}, subscribe, ["$SYS/#"]}.

Allow local users to publish and subscribe all topics:

.. code-block:: erlang

    {allow, {ipaddr, "127.0.0.1"}, pubsub, ["$SYS/#", "#"]}.

Deny users other than local users to subscribe to  topics ``$SYS/#`` and ``#``:

.. code-block:: erlang

    {deny, all, subscribe, ["$SYS/#", {eq, "#"}]}.

Allow any access other than the above rules:

.. code-block:: erlang

    {allow, all}.

.. NOTE:: The default rule only allows local users to subscribe to $SYS/# and与 #.

When the EMQ X broker receives an Publish or Subscribe request from MQTT client, it will match the ACL rule one by one until the match returns to allow or deny.

Set the detection strategy of flapping:

.. code-block:: properties

    ## flapping_detect_policy = <Threshold>, <Duration>, <Banned Interval>
    ## <Threshold>: Specify the maximum number of times the MQTT client can connect repeatedly in <Duration> time
    ## <Duration>: Specify the time window for flapping detection
    ## <Banned Interval>: Specify the interval that the MQTT client is refused to connect after exceeding the connection limit
    flapping_detect_policy = 30, 1m, 5m

-------------------------------------
MQTT Protocol Parameter Configuration
-------------------------------------

MQTT maximum packet size:

.. code-block:: properties

    mqtt.max_packet_size = 1MB

Maximum length of ClientId:

.. code-block:: properties

    mqtt.max_clientid_len = 65535

Maximum level of Topic, 0 means no limit:

.. code-block:: properties

    mqtt.max_topic_levels = 0

Maximum allowed QoS:

.. code-block:: properties

    mqtt.max_qos_allowed = 2

Maximum number of Topic Alias , 0 means Topic Alias is not supported:

.. code-block:: properties

    mqtt.max_topic_alias = 0

Whether to support MQTT messages retain:

.. code-block:: properties

    mqtt.retain_available = true

Whether to support MQTT wildcard subscriptions:

.. code-block:: properties

    mqtt.wildcard_subscription = true

Whether to support MQTT shared subscriptions:

.. code-block:: properties

    mqtt.shared_subscription = true

Whether to allow the loop deliver of the message:

.. code-block:: properties

    mqtt.ignore_loop_deliver = false

This configuration is mainly used to implement (backporting) the No Local feature in MQTT v3.1.1. This feature is standardized in MQTT 5.

Whether to parse MQTT frame with strict mode:

.. code-block:: properties

    mqtt.strict_mode = false

----------------------------------
MQTT Zones Parameter Configuration
----------------------------------

EMQ X uses **Zone** to manage configuration groups. A Zone defines a set of configuration items (such as the maximum number of connections, etc.), and Listener can specify to use a Zone to use all the configurations under that Zone. Multiple Listeners can share the same Zone.

Listeners are configured as follows, with priority Zone > Global > Default:::

                       ---------              ----------              -----------
    Listeners -------> | Zone  | --nomatch--> | Global | --nomatch--> | Default |
                       ---------              ----------              -----------
                           |                       |                       |
                         match                   match                   match
                          \|/                     \|/                     \|/
                    Zone Configs            Global Configs           Default Configs

A zone config has a form ``zone.$name.xxx``, here the ``$name`` is the zone name. Such as ``zone.internal.xxx`` and ``zone.external.xxx``. User can also define customized zone name.

External Zone  Parameter Settings
---------------------------------

The maximum time to wait for MQTT CONNECT packet after the TCP connection is established:

.. code-block:: properties

    zone.external.idle_timeout = 15s

Message Publish rate limit:

.. code-block:: properties

    ## zone.external.publish_limit = 10,100

Enable ACL check:

.. code-block:: properties

    zone.external.enable_acl = on

Enable blacklist checking:

.. code-block:: properties

    zone.external.enable_ban = on

Whether to statistics the information of each connection:

.. code-block:: properties

    zone.external.enable_stats = on

The action when acl check reject current operation:

.. code-block:: properties

    ## Value: ignore | disconnect
    zone.external.acl_deny_action = ignore

Force MQTT connection/session process GC after this number of messages | bytes passed through.:

.. code-block:: properties

    zone.external.force_gc_policy = 1000|1MB

Max message queue length and total heap size to force shutdown connection/session process.:

.. code-block:: properties

    ## zone.external.force_shutdown_policy = 8000|800MB

MQTT maximum packet size:

.. code-block:: properties

    ## zone.external.max_packet_size = 64KB

ClientId maximum length:

.. code-block:: properties

    ## zone.external.max_clientid_len = 1024

Topic maximum level, 0 means no limit:

.. code-block:: properties

    ## zone.external.max_topic_levels = 7

Maximum allowed QoS:

.. code-block:: properties

    ## zone.external.max_qos_allowed = 2

Maximum number of Topic Alias , 0 means Topic Alias is not supported:

.. code-block:: properties

    ## zone.external.max_topic_alias = 0

Whether to support MQTT messages retain:

.. code-block:: properties

    ## zone.external.retain_available = true

Whether to support MQTT wildcard subscriptions:

.. code-block:: properties

    ## zone.external.wildcard_subscription = false

Whether to support MQTT shared subscriptions:

.. code-block:: properties

    ## zone.external.shared_subscription = false

The connection time allowed by the server, Commenting this line means that the connection time is determined by the client:

.. code-block:: properties

    ## zone.external.server_keepalive = 0

Keepalive * backoff * 2 is the actual keep-alive time:

.. code-block:: properties

    zone.external.keepalive_backoff = 0.75

The maximum number of allowed topic subscriptions , 0 means no limit:

.. code-block:: properties

    zone.external.max_subscriptions = 0

Whether to allow QoS upgrade:

.. code-block:: properties

    zone.external.upgrade_qos = off

The maximum size of the in-flight window:

.. code-block:: properties

    zone.external.max_inflight = 32

Resend interval for QoS 1/2 messages:

.. code-block:: properties

    zone.external.retry_interval = 30s

The maximum number of QoS2 messages waiting for PUBREL (Client -> Broker), 0 means no limit:

.. code-block:: properties

    zone.external.max_awaiting_rel = 100

Maximum time to wait for PUBREL before QoS2 message (Client -> Broker) is deleted

.. code-block:: properties

    zone.external.await_rel_timeout = 300s

Default session expiration time used in MQTT v3.1.1 connections:

.. code-block:: properties

    zone.external.session_expiry_interval = 2h

Maximum length of the message queue:

.. code-block:: properties

    zone.external.max_mqueue_len = 1000

Topic priority:

.. code-block:: properties

    ## zone.external.mqueue_priorities = topic/1=10,topic/2=8

Whether the message queue stores QoS0 messages:

.. code-block:: properties

    zone.external.mqueue_store_qos0 = true

Whether to enable flapping detection:

.. code-block:: properties

    zone.external.enable_flapping_detect = off

mountpoint:

.. code-block:: properties

    ## zone.external.mountpoint = devicebound/

Whether use username replace client id:

.. code-block:: properties

    zone.external.use_username_as_clientid = false

Whether to allow the loop deliver of the message:

.. code-block:: properties

    zone.external.ignore_loop_deliver = false

Whether to parse MQTT frame with strict mode:

.. code-block:: properties

    zone.external.strict_mode = false

Internal Zone Parameter Settings
--------------------------------

Allow anonymous access:

.. code-block:: properties

    zone.internal.allow_anonymous = true

Whether to Statistics the information of each connection:

.. code-block:: properties

    zone.internal.enable_stats = on

Close ACL checking:

.. code-block:: properties

    zone.internal.enable_acl = off

The action when acl check reject current operation:

.. code-block:: properties

    ## Value: ignore | disconnect
    zone.internal.acl_deny_action = ignore

Whether to support MQTT wildcard subscriptions:

.. code-block:: properties

    ## zone.internal.wildcard_subscription = true

Whether to support MQTT shared subscriptions:

.. code-block:: properties

    ## zone.internal.shared_subscription = true

The maximum number of  allowed topic subscriptions, 0 means no limit:

.. code-block:: properties

    zone.internal.max_subscriptions = 0

The maximum size of the in-flight window:

.. code-block:: properties

    zone.internal.max_inflight = 32

The maximum number of QoS2 messages waiting for PUBREL (Client -> Broker), 0 means no limit:

.. code-block:: properties

    zone.internal.max_awaiting_rel = 100

Maximum length of the message queue:

.. code-block:: properties

    zone.internal.max_mqueue_len = 1000

Whether the message queue stores QoS0 messages:

.. code-block:: properties

    zone.internal.mqueue_store_qos0 = true

Whether to enable flapping detection:

.. code-block:: properties

    zone.internal.enable_flapping_detect = off

mountpoint:

.. code-block:: properties

    ## zone.internal.mountpoint = devicebound/

Whether use username replace client id:

.. code-block:: properties

    zone.internal.use_username_as_clientid = false

Whether to allow the loop deliver of the message:

.. code-block:: properties

    zone.internal.ignore_loop_deliver = false

Whether to parse MQTT frame with strict mode:

.. code-block:: properties

    zone.internal.strict_mode = false

------------------------------------
MQTT Listeners Parameter Description
------------------------------------

The EMQ X message server supports the MQTT, MQTT/SSL, and MQTT/WS protocol, the port, maximum allowed connections, and other parameters are configurable through ``listener.tcp|ssl|ws|wss|.*``.

The TCP ports of the EMQ X broker that are enabled by default include:

+------+------------------------------+
| 1883 | MQTT TCP protocol port       |
+------+------------------------------+
| 8883 | MQTT/TCP SSL port            |
+------+------------------------------+
| 8083 | MQTT/WebSocket port          |
+------+------------------------------+
| 8084 | MQTT/WebSocket with SSL port |
+------+------------------------------+

Listener parameter description:

+----------------------------------------+----------------------------------------------+
| listener.tcp.${name}.acceptors         | TCP Acceptor pool                            |
+----------------------------------------+----------------------------------------------+
| listener.tcp.${name}.max_connections   | Maximum number of allowed TCP connections    |
+----------------------------------------+----------------------------------------------+
| listener.tcp.${name}.max_conn_rate     | Connection limit configuration               |
+----------------------------------------+----------------------------------------------+
| listener.tcp.${name}.zone              | To which zone the listener belongs           |
+----------------------------------------+----------------------------------------------+
| listener.tcp.${name}.rate_limit        | Connection rate configuration                |
+----------------------------------------+----------------------------------------------+

-------------------------
MQTT/TCP Listener - 1883
-------------------------

The EMQ X supports the configuration of multiple MQTT protocol listeners, for example, two listeners named ``external`` and ``internal``:

TCP listeners:

.. code-block:: properties

    listener.tcp.external = 0.0.0.0:1883

Receive pool size:

.. code-block:: properties

    listener.tcp.external.acceptors = 8

Maximum number of concurrent connections:

.. code-block:: properties

    listener.tcp.external.max_connections = 1024000

Maximum number of connections created per second:

.. code-block:: properties

    listener.tcp.external.max_conn_rate = 1000

Zone used by the listener:

.. code-block:: properties

    listener.tcp.external.zone = external

TCP data receive rate limit:

.. code-block:: properties

    ## listener.tcp.external.rate_limit = 100KB,10s

Access control rules:

.. code-block:: properties

    listener.tcp.external.access.1 = allow all

Whether the proxy protocol V1/2 is enabled when the EMQ X cluster is deployed with HAProxy or Nginx:

.. code-block:: properties

    ## listener.tcp.external.proxy_protocol = on

Timeout of the proxy protocol:

.. code-block:: properties

    ## listener.tcp.external.proxy_protocol_timeout = 3s

Enable the X.509 certificate-based authentication option. EMQ X will use the common name of the certificate as the MQTT username:

.. code-block:: properties

    ## listener.tcp.external.peer_cert_as_username = cn

The maximum length of the queue of suspended connection:

.. code-block:: properties

    listener.tcp.external.backlog = 1024

TCP send timeout:

.. code-block:: properties

    listener.tcp.external.send_timeout = 15s

Whether to close the TCP connection when the sent is timeout:

.. code-block:: properties

    listener.tcp.external.send_timeout_close = on

TCP receive buffer (os kernel) for MQTT connections:

.. code-block:: properties

    #listener.tcp.external.recbuf = 2KB

TCP send buffer (os kernel) for MQTT connections:

.. code-block:: properties

    #listener.tcp.external.sndbuf = 2KB

The size of the user-level software buffer used by the driver should not to be confused with the options of sndbuf and recbuf, which correspond to the kernel socket buffer.
It is recommended to use val(buffer) >= max(val(sndbuf), val(recbuf)) to avoid performance problems caused by unnecessary duplication.
When the sndbuf or recbuf value is set, val(buffer) is automatically set to the maximum value abovementioned:

.. code-block:: properties

    #listener.tcp.external.buffer = 2KB

Whether to set buffer = max(sndbuf, recbuf):

.. code-block:: properties

    ## listener.tcp.external.tune_buffer = off

Whether to set the TCP_NODELAY flag. If this flag is set, it will attempt to send data once there is data in the send buffer.

.. code-block:: properties

    listener.tcp.external.nodelay = true

Whether to set the SO_REUSEADDR flag:

.. code-block:: properties

    listener.tcp.external.reuseaddr = true

-------------------------
MQTT/SSL Listener - 8883
-------------------------

SSL listening port:

.. code-block:: properties

    listener.ssl.external = 8883

Acceptor size:

.. code-block:: properties

    listener.ssl.external.acceptors = 16

Maximum number of concurrent connections:

.. code-block:: properties

    listener.ssl.external.max_connections = 102400

Maximum number of connections created per second:

.. code-block:: properties

    listener.ssl.external.max_conn_rate = 500

Specify the {active, N} options for SSL:

.. code-block:: properties

    listener.ssl.external.active_n = 100

Zone used by the listener:

.. code-block:: properties

    listener.ssl.external.zone = external

TCP data receive rate limit:

.. code-block:: properties

    ## listener.ssl.external.rate_limit = 100KB,10s

Access control rules:

.. code-block:: properties

    listener.ssl.external.access.1 = allow all

Whether the proxy protocol V1/2 is enabled when the EMQ X cluster is deployed with HAProxy or Nginx:

.. code-block:: properties

    ## listener.ssl.external.proxy_protocol = on

Timeout of the proxy protocol:

.. code-block:: properties

    ## listener.ssl.external.proxy_protocol_timeout = 3s

TLS version to prevent POODLE attacks:

.. code-block:: properties

    ## listener.ssl.external.tls_versions = tlsv1.2,tlsv1.1,tlsv1

TLS handshake timeout:

.. code-block:: properties

    listener.ssl.external.handshake_timeout = 15s

Path of the file containing the user's private key:

.. code-block:: properties

    listener.ssl.external.keyfile = etc/certs/key.pem

Path of the file containing the user certificate:

.. code-block:: properties

    listener.ssl.external.certfile = etc/certs/cert.pem

Path of the file containing the CA certificate:

.. code-block:: properties

    ## listener.ssl.external.cacertfile = etc/certs/cacert.pem

Path of the file containing dh-params:

.. code-block:: properties

    ## listener.ssl.external.dhfile = etc/certs/dh-params.pem

Configure verify mode, and the server only performs x509 path verification in verify_peer mode and sends a certificate request to the client:

.. code-block:: properties

    ## listener.ssl.external.verify = verify_peer

When the server is in the verify_peer mode,  whether the server returns a failure if the client does not have a certificate to send:

.. code-block:: properties

    ## listener.ssl.external.fail_if_no_peer_cert = true

SSL cipher suites:

.. code-block:: properties

    listener.ssl.external.ciphers = ECDHE-ECDSA-AES256-GCM-SHA384,ECDHE-RSA-AES256-GCM-SHA384,ECDHE-ECDSA-AES256-SHA384,ECDHE-RSA-AES256-SHA384,ECDHE-ECDSA-DES-CBC3-SHA,ECDH-ECDSA-AES256-GCM-SHA384,ECDH-RSA-AES256-GCM-SHA384,ECDH-ECDSA-AES256-SHA384,ECDH-RSA-AES256-SHA384,DHE-DSS-AES256-GCM-SHA384,DHE-DSS-AES256-SHA256,AES256-GCM-SHA384,AES256-SHA256,ECDHE-ECDSA-AES128-GCM-SHA256,ECDHE-RSA-AES128-GCM-SHA256,ECDHE-ECDSA-AES128-SHA256,ECDHE-RSA-AES128-SHA256,ECDH-ECDSA-AES128-GCM-SHA256,ECDH-RSA-AES128-GCM-SHA256,ECDH-ECDSA-AES128-SHA256,ECDH-RSA-AES128-SHA256,DHE-DSS-AES128-GCM-SHA256,DHE-DSS-AES128-SHA256,AES128-GCM-SHA256,AES128-SHA256,ECDHE-ECDSA-AES256-SHA,ECDHE-RSA-AES256-SHA,DHE-DSS-AES256-SHA,ECDH-ECDSA-AES256-SHA,ECDH-RSA-AES256-SHA,AES256-SHA,ECDHE-ECDSA-AES128-SHA,ECDHE-RSA-AES128-SHA,DHE-DSS-AES128-SHA,ECDH-ECDSA-AES128-SHA,ECDH-RSA-AES128-SHA,AES128-SHA

SSL PSK cipher suites:

.. code-block:: properties

    ## listener.ssl.external.psk_ciphers = PSK-AES128-CBC-SHA,PSK-AES256-CBC-SHA,PSK-3DES-EDE-CBC-SHA,PSK-RC4-SHA

Whether to start a more secure renegotiation mechanism:

.. code-block:: properties

    ## listener.ssl.external.secure_renegotiate = off

Whether to allow the client to reuse an existing session:

.. code-block:: properties

    ## listener.ssl.external.reuse_sessions = on

Whether to force ciphers to be set in the order specified by the server, not by the client:

.. code-block:: properties

    ## listener.ssl.external.honor_cipher_order = on

Use the CN, EN, or CRT field in the client certificate as the username. Note that "verify" should be set to "verify_peer":

.. code-block:: properties

    ## listener.ssl.external.peer_cert_as_username = cn

The maximum length of the queue of suspended connection:

.. code-block:: properties

    ## listener.ssl.external.backlog = 1024

TCP send timeout:

.. code-block:: properties

    ## listener.ssl.external.send_timeout = 15s

Whether to close the TCP connection when the sent is timeout:

.. code-block:: properties

    ## listener.ssl.external.send_timeout_close = on

TCP receive buffer (os kernel) for MQTT connections:

.. code-block:: properties

    #listener.ssl.external.recbuf = 2KB

TCP send buffer (os kernel) for MQTT connections:

.. code-block:: properties

    ## listener.ssl.external.sndbuf = 4KB

The size of the user-level software buffer used by the driver should not to be confused with the options of sndbuf and recbuf, which correspond to the kernel socket buffer.
It is recommended to use val(buffer) >= max(val(sndbuf), val(recbuf)) to avoid performance problems caused by unnecessary duplication.
When the sndbuf or recbuf value is set, val(buffer) is automatically set to the maximum value abovementioned:

.. code-block:: properties

    ## listener.ssl.external.buffer = 4KB

Whether to set buffer = max(sndbuf, recbuf):

.. code-block:: properties

    ## listener.ssl.external.tune_buffer = off

Whether to set the TCP_NODELAY flag. If this flag is set, it will attempt to send data once there is data in the send buffer:

.. code-block:: properties

    ## listener.ssl.external.nodelay = true

Whether to set the SO_REUSEADDR flag:

.. code-block:: properties

    listener.ssl.external.reuseaddr = true

------------------------------
MQTT/WebSocket Listener - 8083
------------------------------

MQTT/WebSocket listening port:

.. code-block:: properties

    listener.ws.external = 8083

The path of WebSocket MQTT endpoint:

.. code-block:: properties

    listener.ws.external.mqtt_path = /mqtt

Acceptors size:

.. code-block:: properties

    listener.ws.external.acceptors = 4

Maximum number of concurrent connections:

.. code-block:: properties

    listener.ws.external.max_connections = 102400

Maximum number of connections created per second:

.. code-block:: properties

    listener.ws.external.max_conn_rate = 1000

TCP data receive rate limit:

.. code-block:: properties

    ## listener.ws.external.rate_limit = 100KB,10s

Zone used by the listener:

.. code-block:: properties

    listener.ws.external.zone = external

Access control rules:

.. code-block:: properties

    listener.ws.external.access.1 = allow all

Whether to verify that the protocol header is valid:

.. code-block:: properties

    listener.ws.external.verify_protocol_header = on

Uses X-Forward-For to identify the original IP after the EMQ X cluster is deployed with NGINX or HAProxy:

.. code-block:: properties

    ## listener.ws.external.proxy_address_header = X-Forwarded-For

Uses X-Forward-For to identify the original port after the EMQ X cluster is deployed with NGINX or HAProxy:

.. code-block:: properties

    ## listener.ws.external.proxy_port_header = X-Forwarded-Port

Whether the proxy protocol V1/2 is enabled when the EMQ X cluster is deployed with HAProxy or Nginx:

.. code-block:: properties

    ## listener.ws.external.proxy_protocol = on

Proxy protocol timeout:

.. code-block:: properties

    ## listener.ws.external.proxy_protocol_timeout = 3s

The maximum length of the queue of suspended connection:

.. code-block:: properties

    listener.ws.external.backlog = 1024

TCP send timeout:

.. code-block:: properties

    listener.ws.external.send_timeout = 15s

Whether to close the TCP connection when the send is timeout:

.. code-block:: properties

    listener.ws.external.send_timeout_close = on

TCP receive buffer (os kernel) for MQTT connections:

.. code-block:: properties

    ## listener.ws.external.recbuf = 2KB

TCP send buffer (os kernel) for MQTT connections:

.. code-block:: properties

    ## listener.ws.external.sndbuf = 2KB

The size of the user-level software buffer used by the driver should not to be confused with the options of sndbuf and recbuf, which correspond to the kernel socket buffer.
It is recommended to use val(buffer) >= max(val(sndbuf), val(recbuf)) to avoid performance problems caused by unnecessary duplication.
When the sndbuf or recbuf value is set, val(buffer) is automatically set to the maximum value abovementioned:

.. code-block:: properties

    ## listener.ws.external.buffer = 2KB

Whether to set buffer  = max(sndbuf, recbuf):

.. code-block:: properties

    ## listener.ws.external.tune_buffer = off

Whether to set the TCP_NODELAY flag. If this flag is set, it will attempt to send data once there is data in the send buffer:

.. code-block:: properties

    listener.ws.external.nodelay = true

Whether to compress Websocket messages:

.. code-block:: properties

    ## listener.ws.external.compress = true

Websocket deflate option:

.. code-block:: properties

    ## listener.ws.external.deflate_opts.level = default
    ## listener.ws.external.deflate_opts.mem_level = 8
    ## listener.ws.external.deflate_opts.strategy = default
    ## listener.ws.external.deflate_opts.server_context_takeover = takeover
    ## listener.ws.external.deflate_opts.client_context_takeover = takeover
    ## listener.ws.external.deflate_opts.server_max_window_bits = 15
    ## listener.ws.external.deflate_opts.client_max_window_bits = 15

Maximum idle time:

.. code-block:: properties

    ## listener.ws.external.idle_timeout = 60s

Maximum packet size, 0 means no limit:

.. code-block:: properties

    ## listener.ws.external.max_frame_size = 0

---------------------------------------
MQTT/WebSocket with SSL Listener - 8084
---------------------------------------

MQTT/WebSocket with SSL listening port:

.. code-block:: properties

    listener.wss.external = 8084

The path of WebSocket MQTT endpoint:

.. code-block:: properties

    listener.wss.external.mqtt_path = /mqtt

Acceptors size:

.. code-block:: properties

    listener.wss.external.acceptors = 4

Maximum number of concurrent connections:

.. code-block:: properties

    listener.wss.external.max_connections = 16

Maximum number of connections created per second:

.. code-block:: properties

    listener.wss.external.max_conn_rate = 1000

Specify the {active, N} options for SSL:

.. code-block:: properties

    listener.wss.external.active_n = 100

TCP data receive rate limit:

.. code-block:: properties

    ## listener.wss.external.rate_limit = 100KB,10s

Zone used by the listener:

.. code-block:: properties

    listener.wss.external.zone = external

Access control rules:

.. code-block:: properties

    listener.wss.external.access.1 = allow all

Whether to verify that the protocol header is valid:

.. code-block:: properties

    listener.wss.external.verify_protocol_header = on

Uses X-Forward-For to identify the original IP after the EMQ X cluster is deployed with NGINX or HAProxy:

.. code-block:: properties

    ## listener.wss.external.proxy_address_header = X-Forwarded-For

Uses X-Forward-For to identify the original port after the EMQ X cluster is deployed with NGINX or HAProxy:

.. code-block:: properties

    ## listener.wss.external.proxy_port_header = X-Forwarded-Port

Whether the proxy protocol V1/2 is enabled when the EMQ X cluster is deployed with HAProxy or Nginx:

.. code-block:: properties

    ## listener.wss.external.proxy_protocol = on

Proxy protocol timeout:

.. code-block:: properties

    ## listener.wss.external.proxy_protocol_timeout = 3s

TLS version to prevent POODLE attacks:

.. code-block:: properties

    ## listener.wss.external.tls_versions = tlsv1.2,tlsv1.1,tlsv1

Path of the file containing the user's private key:

.. code-block:: properties

    listener.wss.external.keyfile = etc/certs/key.pem

Path of the file containing the user certificate:

.. code-block:: properties

    listener.wss.external.certfile = etc/certs/cert.pem

Path of the file containing the CA certificate:

.. code-block:: properties

    ## listener.wss.external.cacertfile = etc/certs/cacert.pem

Path of the file containing dh-params:

.. code-block:: properties

    ## listener.ssl.external.dhfile = etc/certs/dh-params.pem

Configure verify mode, and the server only performs x509 path verification in verify_peer mode and sends a certificate request to the client:

.. code-block:: properties

    ## listener.wss.external.verify = verify_peer

When the server is in the verify_peer mode, whether the server returns a failure if the client does not have a certificate to send :

.. code-block:: properties

    ## listener.wss.external.fail_if_no_peer_cert = true

SSL cipher suites:

.. code-block:: properties

    ## listener.wss.external.ciphers = ECDHE-ECDSA-AES256-GCM-SHA384,ECDHE-RSA-AES256-GCM-SHA384,ECDHE-ECDSA-AES256-SHA384,ECDHE-RSA-AES256-SHA384,ECDHE-ECDSA-DES-CBC3-SHA,ECDH-ECDSA-AES256-GCM-SHA384,ECDH-RSA-AES256-GCM-SHA384,ECDH-ECDSA-AES256-SHA384,ECDH-RSA-AES256-SHA384,DHE-DSS-AES256-GCM-SHA384,DHE-DSS-AES256-SHA256,AES256-GCM-SHA384,AES256-SHA256,ECDHE-ECDSA-AES128-GCM-SHA256,ECDHE-RSA-AES128-GCM-SHA256,ECDHE-ECDSA-AES128-SHA256,ECDHE-RSA-AES128-SHA256,ECDH-ECDSA-AES128-GCM-SHA256,ECDH-RSA-AES128-GCM-SHA256,ECDH-ECDSA-AES128-SHA256,ECDH-RSA-AES128-SHA256,DHE-DSS-AES128-GCM-SHA256,DHE-DSS-AES128-SHA256,AES128-GCM-SHA256,AES128-SHA256,ECDHE-ECDSA-AES256-SHA,ECDHE-RSA-AES256-SHA,DHE-DSS-AES256-SHA,ECDH-ECDSA-AES256-SHA,ECDH-RSA-AES256-SHA,AES256-SHA,ECDHE-ECDSA-AES128-SHA,ECDHE-RSA-AES128-SHA,DHE-DSS-AES128-SHA,ECDH-ECDSA-AES128-SHA,ECDH-RSA-AES128-SHA,AES128-SHA

SSL PSK cipher suites:

.. code-block:: properties

    ## listener.wss.external.psk_ciphers = PSK-AES128-CBC-SHA,PSK-AES256-CBC-SHA,PSK-3DES-EDE-CBC-SHA,PSK-RC4-SHA

Whether to enable a more secure renegotiation mechanism:

.. code-block:: properties

    ## listener.wss.external.secure_renegotiate = off

Whether to allow the client to reuse an existing session:

.. code-block:: properties

    ## listener.wss.external.reuse_sessions = on

Whether to force ciphers to be set in the order specified by the server, not by the client:

.. code-block:: properties

    ## listener.wss.external.honor_cipher_order = on

Use the CN, EN, or CRT field in the client certificate as the username. Note that "verify" should be set to "verify_peer":

.. code-block:: properties

    ## listener.wss.external.peer_cert_as_username = cn

The maximum length of the queue that suspends the connection:

.. code-block:: properties

    listener.wss.external.backlog = 1024

TCP send timeout:

.. code-block:: properties

    listener.wss.external.send_timeout = 15s

Whether to close the TCP connection when the send is timeout  :

.. code-block:: properties

    listener.wss.external.send_timeout_close = on

TCP receive buffer (os kernel) for MQTT connections:

.. code-block:: properties

    ## listener.wss.external.recbuf = 4KB

TCP send buffer (os kernel) for MQTT connections:

.. code-block:: properties

    ## listener.wss.external.sndbuf = 4KB

The size of the user-level software buffer used by the driver should not to be confused with the options of sndbuf and recbuf, which correspond to the kernel socket buffer.
It is recommended to use val(buffer) >= max(val(sndbuf), val(recbuf)) to avoid performance problems caused by unnecessary duplication.
When the sndbuf or recbuf value is set, val(buffer) is automatically set to the maximum value abovementioned:

.. code-block:: properties

    ## listener.wss.external.buffer = 4KB

Whether to set the TCP_NODELAY flag. If this option is enabled, it will attempt to send data once there is data in the send buffer :

.. code-block:: properties

    ## listener.wss.external.nodelay = true

Whether to compress Websocket messages:

.. code-block:: properties

    ## listener.wss.external.compress = true

Websocket deflate option:

.. code-block:: properties

    ## listener.wss.external.deflate_opts.level = default
    ## listener.wss.external.deflate_opts.mem_level = 8
    ## listener.wss.external.deflate_opts.strategy = default
    ## listener.wss.external.deflate_opts.server_context_takeover = takeover
    ## listener.wss.external.deflate_opts.client_context_takeover = takeover
    ## listener.wss.external.deflate_opts.server_max_window_bits = 15
    ## listener.wss.external.deflate_opts.client_max_window_bits = 15

Maximum idle time:

.. code-block:: properties

    ## listener.wss.external.idle_timeout = 60s

Maximum packet size, 0 means no limit:

.. code-block:: properties

    ## listener.wss.external.max_frame_size = 0

--------
Modules
--------

EMQ X supports module expansion. The three default modules are the online and offline status message publishing module, the proxy subscription module, and the topic rewriting module.

Online and offline status message publishing module
---------------------------------------------------

Whether to enable the online and offline status message publishing module:

.. code-block:: properties

    module.presence = on

QoS used by the online and offline status message publishing module to publish MQTT messages:

.. code-block:: properties

    module.presence.qos = 1

Proxy Subscription Module
-------------------------

Whether to enable the proxy subscription module:

.. code-block:: properties

    module.subscription = off

Topics and QoS that are automatically subscribed when the client connects:

.. code-block:: properties

    ## Subscribe the Topics's qos
    ## module.subscription.1.topic = $client/%c
    ## module.subscription.1.qos = 0
    ## module.subscription.2.topic = $user/%u
    ## module.subscription.2.qos = 1

Topic Rewriting Module
----------------------

Whether to enable the topic rewriting module:

.. code-block:: properties

    module.rewrite = off

Topic rewriting rule:

.. code-block:: properties

    ## module.rewrite.rule.1 = x/# ^x/y/(.+)$ z/y/$1
    ## module.rewrite.rule.2 = y/+/z/# ^y/(.+)/z/(.+)$ y/z/$2

-------------------------------
Configuration Files for Plugins
-------------------------------

The directory where the plugin configuration file is stored:

.. code-block:: properties

    plugins.etc_dir = etc/plugins/

Path of the file to store list of plugins that needs to be automatically loaded  at startup

.. code-block:: properties

    plugins.loaded_file = data/loaded_plugins

The EMQ X plugin configuration file, which is in the directory of ``etc/plugins/`` by default, and can be adjusted by modification of plugins.etc_dir.

-------------------------
Broker Parameter Settings
-------------------------

System message publishing interval:

.. code-block:: properties

    broker.sys_interval = 1m

System heartbeat interval of publishing following heart beat message:

.. code-block:: properties

    broker.sys_heartbeat = 30s

Whether to register the session globally:

.. code-block:: properties

    broker.enable_session_registry = on

Session locking strategy:

.. code-block:: properties

    broker.session_locking_strategy = quorum

Dispatch strategy for shared subscriptions:

.. code-block:: properties

    broker.shared_subscription_strategy = random

Whether an ACK is required when dispatching the sharing subscription:

.. code-block:: properties

    broker.shared_dispatch_ack_enabled = false

Whether to enable route batch cleaning:

.. code-block:: properties

    broker.route_batch_clean = off

--------------------
Erlang VM Monitoring
--------------------

Whether to enable long_gc monitoring and how long garbage collection lasts that can trigger the long_gc event:

.. code-block:: properties

    sysmon.long_gc = false

How long does a process or port in the system keep running to trigger long_schedule event:

.. code-block:: properties

    sysmon.long_schedule = 240

How big is the size of allocated heap caused by garbage collection to trigger the large_heap event:

.. code-block:: properties

    sysmon.large_heap = 8MB

Whether a process in the system triggers a busy_port event when it hangs because it is sent to a busy port:

.. code-block:: properties

    sysmon.busy_port = false

Whether to listen Erlang distributed port busy events:

.. code-block:: properties

    sysmon.busy_dist_port = true

Cpu occupancy check interval:

.. code-block:: properties

    os_mon.cpu_check_interval = 60s

An alarm is generated when the CPU usage is higher than:

.. code-block:: properties

    os_mon.cpu_high_watermark = 80%

Clear the alarm when the CPU usage is lower than:

.. code-block:: properties

    os_mon.cpu_low_watermark = 60%

Memory usage check interval:

.. code-block:: properties

    os_mon.mem_check_interval = 60s

An alarm is generated when the system memory usage is higher than:

.. code-block:: properties

    os_mon.sysmem_high_watermark = 70%

An alarm is generated when the memory usage of a single process is higher than:

.. code-block:: properties

    os_mon.procmem_high_watermark = 5%

The check interval of the number of processes:

.. code-block:: properties

    vm_mon.check_interval = 30s

An alarm is generated when the ratio of the current number of processes to the maximum number of processes reached:

.. code-block:: properties

    vm_mon.process_high_watermark = 80%

Clear the alarm when the ratio of the current number of processes to the maximum number of processes reached:

.. code-block:: properties

    vm_mon.process_low_watermark = 60%
