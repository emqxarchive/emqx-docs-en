
.. _guide:

==========
User Guide
==========

--------------
Authentication
--------------

The *EMQ X* broker supports to authenticate MQTT clients via ClientID, Username/Password, IpAddress and even HTTP Cookies.

The authentication is provided by a list of plugins such as MySQL, PostgresSQL, Redis, MongoDB, HTTP, LDAP.

If we enable several authentication plugins at the same time, the Auth request will be forwarded to next auth module

The authentication flow::

              ---------------            --------------            ---------------
  Client --> |  Redis Auth   | -ignore-> |  HTTP Auth  | -ignore-> |  MySQL Auth  |
              ---------------            --------------            ---------------
                    |                           |                         |
                   \|/                         \|/                       \|/
              allow | deny                allow | deny              allow | deny


The order that the authentication will be carried out is determined by the order that the plugins are loaded (their order of appearance in ``./data/loaded_plugins``)

The authentication plugins implemented by default:

+---------------------------+---------------------------+
| Plugin                    | Description               |
+===========================+===========================+
| `emqx_auth_clientid`_     | ClientId Auth Plugin      |
+---------------------------+---------------------------+
| `emqx_auth_username`_     | Username Auth Plugin      |
+---------------------------+---------------------------+
| `emqx_auth_ldap`_         | LDAP Auth Plugin          |
+---------------------------+---------------------------+
| `emqx_auth_http`_         | HTTP Auth/ACL Plugin      |
+---------------------------+---------------------------+
| `emqx_auth_mysql`_        | MySQL Auth/ACL Plugin     |
+---------------------------+---------------------------+
| `emqx_auth_pgsql`_        | PgSQL Auth/ACL Plugin     |
+---------------------------+---------------------------+
| `emqx_auth_redis`_        | Redis Auth/ACL Plugin     |
+---------------------------+---------------------------+
| `emqx_auth_mongo`_        | MongoDB Auth/ACL Plugin   |
+---------------------------+---------------------------+
| `emqx_auth_jwt`_          | JWT Auth/ACL Plugin       |
+---------------------------+---------------------------+

---------------
Allow Anonymous
---------------

Configure etc/emqx.conf to allow anonymous authentication:

.. code-block:: properties

    ## Allow Anonymous authentication
    allow_anonymous = true

Username/Password
-----------------

Authenticate MQTT client with Username/Password:

Configure default users in etc/plugins/emqx_auth_username.conf:

.. code-block:: properties

    auth.user.$N.username = admin
    auth.user.$N.password = public

Enable `emqx_auth_username`_ plugin:

.. code-block:: bash

    ./bin/emqx_ctl plugins load emqx_auth_username

Add user by './bin/emqx_ctl users' command::

   $ ./bin/emqx_ctl users add <Username> <Password>

ClientId
---------

Authentication with MQTT ClientId.

Configure Client Ids in etc/plugins/emqx_auth_clientid.conf:

.. code-block:: properties

    auth.client.$N.clientid = clientid
    auth.client.$N.password = passwd

Enable `emqx_auth_clientid`_ plugin:

.. code-block:: bash

    ./bin/emqx_ctl plugins load emqx_auth_clientid

LDAP
----

etc/plugins/emqx_auth_ldap.conf:

.. code-block:: properties

    ## LDAP server list, seperated by ','.
    ## Value: String
    auth.ldap.servers = 127.0.0.1

    ## LDAP server port.
    ## Value: Port
    auth.ldap.port = 389

    ## LDAP Bind DN.
    ## Value: DN
    auth.ldap.bind_dn = cn=root,dc=emqtt,dc=com

    ## LDAP Bind Password.
    ## Value: String
    auth.ldap.bind_password = public

    ## LDAP query timeout.
    ## Value: Number
    auth.ldap.timeout = 30

    ## Authentication DN.
    ##  -%u: username
    ##  -%c: clientid
    ##
    ## Value: DN
    auth.ldap.auth_dn = cn=%u,ou=auth,dc=emqtt,dc=com

    ## Password hash.
    ## Value: plain | md5 | sha | sha256
    auth.ldap.password_hash = sha256

    ## Whether to enable SSL.
    ## Value: true | false
    auth.ldap.ssl = false

Enable LDAP plugin::

    ./bin/emqx_ctl plugins load emqx_auth_ldap

HTTP
----

etc/plugins/emqx_auth_http.conf:

.. code-block:: properties

    ## Variables: %u = username, %c = clientid, %a = ipaddress, %P = password, %t = topic

    auth.http.auth_req = http://127.0.0.1:8080/mqtt/auth
    auth.http.auth_req.method = post
    auth.http.auth_req.params = clientid=%c,username=%u,password=%P

    auth.http.super_req = http://127.0.0.1:8080/mqtt/superuser
    auth.http.super_req.method = post
    auth.http.super_req.params = clientid=%c,username=%u

Enable HTTP Plugin::

    ./bin/emqx_ctl plugins load emqx_auth_http


JWT
----

etc/plugins/emqx_auth_jwt.conf:

.. code-block:: properties

    ##--------------------------------------------------------------------
    ## JWT Auth Plugin
    ##--------------------------------------------------------------------

    ## HMAC Hash Secret.
    ##
    ## Value: String
    auth.jwt.secret = emqxsecret

    ## RSA or ECDSA public key file.
    ##
    ## Value: File
    ## auth.jwt.pubkey = etc/certs/jwt_public_key.pem

Enable JWT plugin::

    ./bin/emqx_ctl plugins load emqx_auth_jwt

MySQL
-----

Authenticate with MySQL database. Suppose that we create a mqtt_user table:

.. code-block:: sql

    CREATE TABLE `mqtt_user` (
      `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
      `username` varchar(100) DEFAULT NULL,
      `password` varchar(100) DEFAULT NULL,
      `salt` varchar(20) DEFAULT NULL,
      `created` datetime DEFAULT NULL,
      PRIMARY KEY (`id`),
      UNIQUE KEY `mqtt_username` (`username`)
    ) ENGINE=MyISAM DEFAULT CHARSET=utf8;

Configure the 'auth_query' and 'password_hash' in etc/plugins/emqx_auth_mysql.conf:

.. code-block:: properties

    ## Mysql Server
    auth.mysql.server = 127.0.0.1:3306

    ## Mysql Pool Size
    auth.mysql.pool = 8

    ## Mysql Username
    ## auth.mysql.username =

    ## Mysql Password
    ## auth.mysql.password =

    ## Mysql Database
    auth.mysql.database = mqtt

    ## Variables: %u = username, %c = clientid

    ## Authentication Query: select password only
    auth.mysql.auth_query = select password from mqtt_user where username = '%u' limit 1

    ## Password hash: plain, md5, sha, sha256, pbkdf2
    auth.mysql.password_hash = sha256

    ## %% Superuser Query
    auth.mysql.super_query = select is_superuser from mqtt_user where username = '%u' limit 1

Enable MySQL plugin:

.. code-block:: bash

    ./bin/emqx_ctl plugins load emqx_auth_mysql

PostgresSQL
-----------

Authenticate with PostgresSQL database. Create a mqtt_user table:

.. code-block:: sql

    CREATE TABLE mqtt_user (
      id SERIAL primary key,
      is_superuser boolean,
      username character varying(100),
      password character varying(100),
      salt character varying(40)
    );

Configure the 'auth_query' and 'password_hash' in etc/plugins/emqx_auth_pgsql.conf:

.. code-block:: properties

    ## Postgres Server
    auth.pgsql.server = 127.0.0.1:5432

    auth.pgsql.pool = 8

    auth.pgsql.username = root

    #auth.pgsql.password =

    auth.pgsql.database = mqtt

    auth.pgsql.encoding = utf8

    auth.pgsql.ssl = false

    ## Variables: %u = username, %c = clientid, %a = ipaddress

    ## Authentication Query: select password only
    auth.pgsql.auth_query = select password from mqtt_user where username = '%u' limit 1

    ## Password hash: plain, md5, sha, sha256, pbkdf2
    auth.pgsql.password_hash = sha256

    ## sha256 with salt prefix
    ## auth.pgsql.password_hash = salt sha256

    ## sha256 with salt suffix
    ## auth.pgsql.password_hash = sha256 salt

    ## Superuser Query
    auth.pgsql.super_query = select is_superuser from mqtt_user where username = '%u' limit 1

Enable the plugin:

.. code-block:: bash

    ./bin/emqx_ctl plugins load emqx_auth_pgsql

Redis
-----

Authenticate with Redis. MQTT users could be stored in redis HASH, the key is "mqtt_user:<Username>".

Configure 'auth_cmd' and 'password_hash' in etc/plugins/emqx_auth_redis.conf:

.. code-block:: properties

    ## Redis server address.
    ##
    ## Value: Port | IP:Port
    ##
    ## Redis Server: 6379, 127.0.0.1:6379, localhost:6379, Redis Sentinel: 127.0.0.1:26379

    ## Redis sentinel cluster name.
    ##
    ## Value: String
    ## auth.redis.sentinel = mymaster

    ## Redis pool size.
    ##
    ## Value: Number
    auth.redis.pool = 8

    ## Redis database no.
    ##
    ## Value: Number
    auth.redis.database = 0

    ## Redis password.
    ##
    ## Value: String
    ## auth.redis.password =

    ## Variables: %u = username, %c = clientid

    ## Authentication Query Command
    auth.redis.auth_cmd = HMGET mqtt_user:%u password

    ## Password hash: plain, md5, sha, sha256, pbkdf2, bcrypt
    auth.redis.password_hash = sha256

    ## sha256 with salt prefix
    ## auth.redis.password_hash = salt,sha256

    ## sha256 with salt suffix
    ## auth.redis.password_hash = sha256,salt

    ## bcrypt with salt prefix
    ## auth.redis.password_hash = salt,bcrypt

    ## pbkdf2 with macfun iterations dklen
    ## macfun: md4, md5, ripemd160, sha, sha224, sha256, sha384, sha512
    ## auth.redis.password_hash = pbkdf2,sha256,1000,20

    ## Superuser Query Command
    auth.redis.super_cmd = HGET mqtt_user:%u is_superuser

Enable Redis plugin:

.. code-block:: bash

    ./bin/emqx_ctl plugins load emqx_auth_redis

MongoDB
-------

Create a `mqtt_user` collection::

    {
        username: "user",
        password: "password hash",
        is_superuser: boolean (true, false),
        created: "datetime"
    }

Configure `super_query`, `auth_query` in etc/plugins/emqx_auth_mongo.conf:

.. code-block:: properties

    ## MongoDB Topology Type.
    ##
    ## Value: single | unknown | sharded | rs
    auth.mongo.type = single

    ## The set name if type is rs.
    ##
    ## Value: String
    ## auth.mongo.rs_set_name =

    ## MongoDB server list.
    ##
    ## Value: String
    ##
    ## Examples: 127.0.0.1:27017,127.0.0.2:27017...
    auth.mongo.server = 127.0.0.1:27017

    ## Mongo Pool Size
    auth.mongo.pool = 8

    ## MongoDB login user.
    ##
    ## Value: String
    ## auth.mongo.login =

    ## MongoDB password.
    ##
    ## Value: String
    ## auth.mongo.password =

    ## MongoDB AuthSource
    ##
    ## Value: String
    ## Default: mqtt
    ## auth.mongo.auth_source = admin

    ## Mongo Database
    auth.mongo.database = mqtt

    ## auth_query
    auth.mongo.auth_query.collection = mqtt_user

    auth.mongo.auth_query.password_field = password

    auth.mongo.auth_query.password_hash = sha256

    auth.mongo.auth_query.selector = username=%u

    ## super_query
    ## Enable superuser query.
    auth.mongo.super_query = on

    auth.mongo.super_query.collection = mqtt_user

    auth.mongo.super_query.super_field = is_superuser

    auth.mongo.super_query.selector = username=%u

Enable MongoDB plugin:

.. code-block:: bash

    ./bin/emqx_ctl plugins load emqx_auth_mongo

.. _acl:

---
ACL
---

The Access Control Lists (ACL) of *EMQ X* broker is responsible for restricting the access to MQTT topics.

The ACL rules define::

    (Allow|Deny) Who (Publish|Subscribe) Topics

Access Control Module of *EMQ X* broker will match the rules one by one::

              ---------              ---------              ---------
    Client -> | Rule1 | --nomatch--> | Rule2 | --nomatch--> | Rule3 | --> Default
              ---------              ---------              ---------
                  |                      |                      |
                match                  match                  match
                 \|/                    \|/                    \|/
            allow | deny           allow | deny           allow | deny

Internal
--------

The internal(default) ACL of *EMQ X* broker is implemented by an 'internal' module.

Enable the 'internal' ACL module in etc/emq.conf:

.. code-block:: properties

    ## ACL nomatch
    acl_nomatch = allow

    ## Default ACL File
    acl_file = etc/acl.conf

The ACL rules of 'internal' module are defined in 'etc/acl.conf' file:

.. code-block:: erlang

    %% Allow user with username 'dashboard' to subscribe '$SYS/#'
    {allow, {user, "dashboard"}, subscribe, ["$SYS/#"]}.

    %% Allow clients from localhost to subscribe and publish to any topics
    {allow, {ipaddr, "127.0.0.1"}, pubsub, ["$SYS/#", "#"]}.

    %% Deny clients to subscribe topics which matches '$SYS/#' and the topic exactly equals to 'abc/#'. But this doesn't deny topics such as 'abc' or 'abc/d'
    {deny, all, subscribe, ["$SYS/#", {eq, "abc/#"}]}.

    %% Allow all by default
    {allow, all}.

HTTP
-----

ACL by HTTP API: https://github.com/emqx/emqx_auth_http

Configure etc/plugins/emqx_auth_http.conf and enable the plugin:

.. code-block:: properties

    ## 'access' parameter: sub = 1, pub = 2
    auth.http.acl_req = http://127.0.0.1:8080/mqtt/acl
    auth.http.acl_req.method = get
    auth.http.acl_req.params = access=%A,username=%u,clientid=%c,ipaddr=%a,topic=%t

MySQL
-----

ACL with MySQL database. The `mqtt_acl` table and default data:

.. code-block:: sql

    CREATE TABLE `mqtt_acl` (
      `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
      `allow` int(1) DEFAULT NULL COMMENT '0: deny, 1: allow',
      `ipaddr` varchar(60) DEFAULT NULL COMMENT 'IpAddress',
      `username` varchar(100) DEFAULT NULL COMMENT 'Username',
      `clientid` varchar(100) DEFAULT NULL COMMENT 'ClientId',
      `access` int(2) NOT NULL COMMENT '1: subscribe, 2: publish, 3: pubsub',
      `topic` varchar(100) NOT NULL DEFAULT '' COMMENT 'Topic Filter',
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;

    INSERT INTO mqtt_acl (id, allow, ipaddr, username, clientid, access, topic)
    VALUES
        (1,1,NULL,'$all',NULL,2,'#'),
        (2,0,NULL,'$all',NULL,1,'$SYS/#'),
        (3,0,NULL,'$all',NULL,1,'eq #'),
        (5,1,'127.0.0.1',NULL,NULL,2,'$SYS/#'),
        (6,1,'127.0.0.1',NULL,NULL,2,'#'),
        (7,1,NULL,'dashboard',NULL,1,'$SYS/#');

Configure 'acl-query' and 'acl_nomatch' in etc/plugins/emqx_auth_mysql.conf:

.. code-block:: properties

    ## ACL Query Command
    auth.mysql.acl_query = select allow, ipaddr, username, clientid, access, topic from mqtt_acl where ipaddr = '%a' or username = '%u' or username = '$all' or clientid = '%c'

PostgresSQL
------------

ACL with PostgresSQL database. The mqtt_acl table and default data:

.. code-block:: sql

    CREATE TABLE mqtt_acl (
      id SERIAL primary key,
      allow integer,
      ipaddr character varying(60),
      username character varying(100),
      clientid character varying(100),
      access  integer,
      topic character varying(100)
    );

    INSERT INTO mqtt_acl (id, allow, ipaddr, username, clientid, access, topic)
    VALUES
        (1,1,NULL,'$all',NULL,2,'#'),
        (2,0,NULL,'$all',NULL,1,'$SYS/#'),
        (3,0,NULL,'$all',NULL,1,'eq #'),
        (5,1,'127.0.0.1',NULL,NULL,2,'$SYS/#'),
        (6,1,'127.0.0.1',NULL,NULL,2,'#'),
        (7,1,NULL,'dashboard',NULL,1,'$SYS/#');

Configure 'acl_query' and 'acl_nomatch' in etc/plugins/emqx_auth_pgsql.conf:

.. code-block:: properties

    ## ACL Query. Comment this query, the acl will be disabled.
    auth.pgsql.acl_query = select allow, ipaddr, username, clientid, access, topic from mqtt_acl where ipaddr = '%a' or username = '%u' or username = '$all' or clientid = '%c'

Redis
-----

ACL with Redis. The ACL rules are stored in a Redis HashSet::

    HSET mqtt_acl:<username> topic1 1
    HSET mqtt_acl:<username> topic2 2
    HSET mqtt_acl:<username> topic3 3

Configure `acl_cmd` and `acl_nomatch` in etc/plugins/emqx_auth_redis.conf:

.. code-block:: properties

    ## ACL Query Command
    auth.redis.acl_cmd = HGETALL mqtt_acl:%u

MongoDB
-------

Store ACL Rules in a `mqtt_acl` collection:

.. code-block:: json

    {
        "username": "username",
        "clientid": "clientid",
        "publish": ["topic1", "topic2"],
        "subscribe": ["subtop1", "subtop2"],
        "pubsub": ["topic/#", "topic1"]
    }

For example, insert rules into `mqtt_acl` collection::

    db.mqtt_acl.insert({username: "test", publish: ["t/1", "t/2"], subscribe: ["user/%u", "client/%c"]})
    db.mqtt_acl.insert({username: "admin", pubsub: ["#"]})

Configure `acl_query` and `acl_nomatch` in etc/plugins/emqx_auth_mongo.conf:

.. code-block:: properties

    ## acl_query
    auth.mongo.acl_query.collection = mqtt_user

    auth.mongo.acl_query.selector = username=%u

----------------------
MQTT Publish/Subscribe
----------------------

MQTT is a an extremely lightweight publish/subscribe messaging protocol designed for IoT, M2M and Mobile applications. Specifications:
`MQTT V3.1.1 <http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.html>`_
`MQTT V5.0 <http://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html>`_

.. image:: _static/images/pubsub_concept.png

Install and start the *EMQ X* broker, and then any MQTT client could connect to the broker, subscribe topics and publish messages. There're lots of `MQTT Client Libraries <https://github.com/mqtt/mqtt.github.io/wiki/libraries>`_ available from the community.

Take mosquitto for example::

    mosquitto_sub -t topic -q 2
    mosquitto_pub -t topic -q 1 -m "Hello, MQTT!"


MQTT Listener of the EMQ X broker is configured in etc/emq.conf:

.. code-block:: properties

    ## TCP Listener: 1883, 127.0.0.1:1883, ::1:1883
    listener.tcp.external = 0.0.0.0:1883

    ## Size of acceptor pool
    listener.tcp.external.acceptors = 8

    ## Maximum number of concurrent clients
    listener.tcp.external.max_connections = 1024000
    ## Maximum external connections per second.
    ##
    ## Value: Number
    listener.tcp.external.max_conn_rate = 1000

MQTT(SSL) Listener, Default Port is 8883:

.. code-block:: properties

    ## SSL Listener: 8883, 127.0.0.1:8883, ::1:8883
    listener.ssl.external = 8883

    ## Size of acceptor pool
    listener.ssl.external.acceptors = 16

    ## Maximum number of concurrent clients
    listener.ssl.external.max_connections = 102400

    ## Maximum MQTT/SSL connections per second.
    ##
    ## Value: Number
    listener.ssl.external.max_conn_rate = 500

----------------
HTTP Publish API
----------------

The *EMQ X* broker provides a HTTP API for applications publishing messages to MQTT clients.

HTTP API: POST http://localhost:8080/api/v3/mqtt/publish

An cURL example::

    curl -v --basic -u user:passwd -H "Content-Type: application/json" -d '{"qos":1, "retain": false, "topic":"world", "payload":"test" , "client_id": "C_1492145414740"}'  -k http://localhost:8080/api/v3/mqtt/publish

Parameters of the HTTP API:

+---------+-----------------------+
| Name    | Description           |
+=========+=======================+
| client  | ClientID              |
+---------+-----------------------+
| qos     | QoS: 0 | 1 | 2        |
+---------+-----------------------+
| retain  | Retain:true | false   |
+---------+-----------------------+
| topic   | Topic                 |
+---------+-----------------------+
| message | Payload               |
+---------+-----------------------+

.. NOTE::

    The API uses ``HTTP Basic Authentication``.

    The url of this API has been changed to 'api/v3/mqtt/publish' in v3.0-beta.1 release. Read the doc in :doc:`/rest`.

-------------------
MQTT Over WebSocket
-------------------

Web browsers could connect to the emqx broker directly by MQTT Over WebSocket.

+-------------------------+----------------------------------------+
| WebSocket URI:          | ws(s)://host:8083/mqtt                 |
+-------------------------+----------------------------------------+
| Sec-WebSocket-Protocol: | 'mqttv3.1', 'mqttv3.1.1' or 'mqttv5.0' |
+-------------------------+----------------------------------------+

The Dashboard plugin provides a test page for WebSocket::

    http://127.0.0.1:18083/websocket.html

Listener of WebSocket and HTTP Publish API is configured in etc/emq.conf:

.. code-block:: properties

    ## MQTT/WebSocket Listener
    listener.ws.external = 8083
    listener.ws.external.acceptors = 4
    listener.ws.external.max_clients = 64
    listener.ws.external.max_conn_rate = 1000

-----------
$SYS Topics
-----------

The *EMQ X* broker periodically publishes internal status, MQTT statistics, metrics and client online/offline status to $SYS/# topics.

The $SYS topic path is prefixed with::

    $SYS/brokers/${node}/

Where '${node}' is the erlang node name of emqx broker. For example::

    $SYS/brokers/emqx@127.0.0.1/version

    $SYS/brokers/emqx@host2/uptime

.. NOTE:: The broker only allows clients from localhost to subscribe $SYS topics by default.

The interval of publishing $SYS messages could be configured in etc/emqx.conf::

    ## System Interval of publishing broker $SYS Messages
    broker.sys_interval = 1m

Broker Version, Uptime and Description
---------------------------------------

+--------------------------------+-----------------------+
| Topic                          | Description           |
+================================+=======================+
| $SYS/brokers                   | Broker nodes          |
+--------------------------------+-----------------------+
| $SYS/brokers/${node}/version   | Broker Version        |
+--------------------------------+-----------------------+
| $SYS/brokers/${node}/uptime    | Broker Uptime         |
+--------------------------------+-----------------------+
| $SYS/brokers/${node}/datetime  | Broker DateTime       |
+--------------------------------+-----------------------+
| $SYS/brokers/${node}/sysdescr  | Broker Description    |
+--------------------------------+-----------------------+

Online/Offline Status of MQTT Client
------------------------------------

The topic path started with: $SYS/brokers/${node}/clients/

+--------------------------+--------------------------------------------+------------------------------------+
| Topic                    | Payload(JSON)                              | Description                        |
+==========================+============================================+====================================+
| ${clientid}/connected    | {ipaddress: "127.0.0.1", username: "test", | Publish when a client connected    |
|                          |  session: false, version: 3, connack: 0,   |                                    |
|                          |  ts: 1432648482}                           |                                    |
+--------------------------+--------------------------------------------+------------------------------------+
| ${clientid}/disconnected | {reason: "keepalive_timeout",              | Publish when a client disconnected |
|                          |  username: "test", ts: 1432749431}         |                                    |
+--------------------------+--------------------------------------------+------------------------------------+

Properties of 'connected' Payload:

.. code-block:: json

    {
        "clientid":    "test"
        "username":    "test",
        "ipaddress":   "127.0.0.1",
        "clean_start": true,
        "proto_ver":   4,
        "proto_name":  "MQTT",
        "keepalive":   60,
        "connack":   0,
        "ts":        1432648482
    }

Properties of 'disconnected' Payload:

.. code-block:: json

    {
        "clientid":   "test"
        "username":   "test",
        "reason":     "normal",
        "ts":         1432648486
    }

Broker Statistics
-----------------

Topic path started with: $SYS/brokers/${node}/stats/

Clients
.......

+---------------------+---------------------------------------------+
| Topic               | Description                                 |
+---------------------+---------------------------------------------+
| connections/count   | Count of current connections                |
+---------------------+---------------------------------------------+
| connections/max     | Max number of current  connections          |
+---------------------+---------------------------------------------+

Sessions
........

+---------------------------+------------------------------------+
| Topic                     | Description                        |
+---------------------------+------------------------------------+
| sessions/count            | Count of current sessions          |
+---------------------------+------------------------------------+
| sessions/max              | Max number of sessions             |
+---------------------------+------------------------------------+
| sessions/persistent/count | Count of persistent sessions       |
+---------------------------+------------------------------------+
| sessions/persistent/max   | Max number of persistent sessions  |
+---------------------------+------------------------------------+

Subscriptions
.............

+----------------------------+---------------------------------------------+
| Topic                      | Description                                 |
+----------------------------+---------------------------------------------+
| subscriptions/shared/max   | Max number of shared subscriptions          |
+----------------------------+---------------------------------------------+
| subscriptions/shared/count | Count of current shared subscriptions       |
+----------------------------+---------------------------------------------+
| subscriptions/max          | Max number of subscriptions                 |
+----------------------------+---------------------------------------------+
| subscriptions/count        | Count of current subscriptions              |
+----------------------------+---------------------------------------------+
| subscribers/max            | Max number of subscribers                   |
+----------------------------+---------------------------------------------+
| subscribers/count          | Count of current subscribers                |
+----------------------------+---------------------------------------------+

Topics
......

+---------------------+---------------------------------------------+
| Topic               | Description                                 |
+---------------------+---------------------------------------------+
| topics/count        | Count of current topics                     |
+---------------------+---------------------------------------------+
| topics/max          | Max number of topics                        |
+---------------------+---------------------------------------------+

Retained
.......................

+---------------------+---------------------------------------------+
| Topic               | Description                                 |
+---------------------+---------------------------------------------+
| retained/count      | Count of current retained messages          |
+---------------------+---------------------------------------------+
| retained/max        | Max number of retained messages             |
+---------------------+---------------------------------------------+

Routes
.................

+---------------------+---------------------------------------------+
| Topic               | Description                                 |
+---------------------+---------------------------------------------+
| routes/count        | Count of current routes                     |
+---------------------+---------------------------------------------+
| routes/max          | Max number of routes                        |
+---------------------+---------------------------------------------+

Broker Metrics
--------------

Topic path started with: $SYS/brokers/${node}/metrics/

Bytes Sent/Received
...................

+---------------------+---------------------------------------------+
| Topic               | Description                                 |
+---------------------+---------------------------------------------+
| bytes/received      | MQTT Bytes Received since broker started    |
+---------------------+---------------------------------------------+
| bytes/sent          | MQTT Bytes Sent since the broker started    |
+---------------------+---------------------------------------------+

Packets Sent/Received
.....................

+--------------------------+---------------------------------------------+
| Topic                    | Description                                 |
+--------------------------+---------------------------------------------+
| packets/received         | Number Of MQTT Packets Received             |
+--------------------------+---------------------------------------------+
| packets/sent             | Number Of MQTT Packets Sent                 |
+--------------------------+---------------------------------------------+
| packets/connect          | Number Of MQTT CONNECT Packets Received     |
+--------------------------+---------------------------------------------+
| packets/connack          | Number Of MQTT CONNACK Packets Sent         |
+--------------------------+---------------------------------------------+
| packets/publish/received | Number Of MQTT PUBLISH Packets Received     |
+--------------------------+---------------------------------------------+
| packets/publish/sent     | Number Of MQTT PUBLISH Packets Sent         |
+--------------------------+---------------------------------------------+
| packets/puback/received  | Number Of MQTT PUBACK Packets Received      |
+--------------------------+---------------------------------------------+
| packets/puback/sent      | Number Of MQTT PUBACK Packets Sent          |
+--------------------------+---------------------------------------------+
| packets/puback/missed    | Number Of MQTT PUBACK Packets Missed        |
+--------------------------+---------------------------------------------+
| packets/pubrec/received  | Number Of MQTT PUBREC Packets Received      |
+--------------------------+---------------------------------------------+
| packets/pubrec/sent      | Number Of MQTT PUBREC Packets Sent          |
+--------------------------+---------------------------------------------+
| packets/pubrec/missed    | Number Of MQTT PUBREC Packets Missed        |
+--------------------------+---------------------------------------------+
| packets/pubrel/received  | Number Of MQTT PUBREL Packets Received      |
+--------------------------+---------------------------------------------+
| packets/pubrel/sent      | Number Of MQTT PUBREL Packets Sent          |
+--------------------------+---------------------------------------------+
| packets/pubrel/missed    | Number Of MQTT PUBREL Packets Missed        |
+--------------------------+---------------------------------------------+
| packets/pubcomp/received | Number Of MQTT PUBCOMP Packets Received     |
+--------------------------+---------------------------------------------+
| packets/pubcomp/sent     | Number Of MQTT PUBCOMP Packets Sent         |
+--------------------------+---------------------------------------------+
| packets/pubcomp/missed   | Number Of MQTT PUBCOMP Packets Missed       |
+--------------------------+---------------------------------------------+
| packets/subscribe        | Number Of MQTT SUBSCRIBE Packets Received   |
+--------------------------+---------------------------------------------+
| packets/suback           | Number Of MQTT SUBACK Packets Sent          |
+--------------------------+---------------------------------------------+
| packets/unsubscribe      | Number Of MQTT UNSUBSCRIBE Packets Received |
+--------------------------+---------------------------------------------+
| packets/unsuback         | Number Of MQTT UNSUBACK Packets Sent        |
+--------------------------+---------------------------------------------+
| packets/pingreq          | Number Of MQTT PINGREQ Packets Received     |
+--------------------------+---------------------------------------------+
| packets/pingresp         | Number Of MQTT PINGRESP Packets Sent        |
+--------------------------+---------------------------------------------+
| packets/disconnect       | Number Of MQTT DISCONNECT Packets Received  |
+--------------------------+---------------------------------------------+
| packets/auth             | Number Of Auth Packets Received             |
+--------------------------+---------------------------------------------+

Messages Sent/Received
......................

+--------------------------+---------------------------------------------+
| Topic                    | Topic                                       |
+--------------------------+---------------------------------------------+
| messages/received        | Number of messages received                 |
+--------------------------+---------------------------------------------+
| messages/sent            | Number of messages sent                     |
+--------------------------+---------------------------------------------+
| messages/expired         | Number of messages expired                  |
+--------------------------+---------------------------------------------+
| messages/retained        | Number of messages retained                 |
+--------------------------+---------------------------------------------+
| messages/dropped         | Number of messages dropped                  |
+--------------------------+---------------------------------------------+
| messages/forward         | Number of messages forward by other nodes   |
+--------------------------+---------------------------------------------+
| messages/qos0/received   | Number of QoS0 messages received            |
+--------------------------+---------------------------------------------+
| messages/qos0/sent       | Number of QoS0 messages sent                |
+--------------------------+---------------------------------------------+
| messages/qos1/received   | Number of QoS1 messages received            |
+--------------------------+---------------------------------------------+
| messages/qos1/sent       | Number of QoS1 messages sent                |
+--------------------------+---------------------------------------------+
| messages/qos2/received   | Number of QoS2 messages received            |
+--------------------------+---------------------------------------------+
| messages/qos2/sent       | Number of QoS2 messages sent                |
+--------------------------+---------------------------------------------+
| messages/qos2/expired    | Number of QoS2 messages expired             |
+--------------------------+---------------------------------------------+
| messages/qos2/dropped    | Number of QoS2 messages dropped             |
+--------------------------+---------------------------------------------+

Broker Alarms
-------------

Topic path started with: $SYS/brokers/${node}/alarms/

+------------------+------------------+
| Topic            | Description      |
+------------------+------------------+
| ${alarmId}/alert | New Alarm        |
+------------------+------------------+
| ${alarmId}/clear | Clear Alarm      |
+------------------+------------------+

Broker Sysmon
-------------

Topic path started with: '$SYS/brokers/${node}/sysmon/'

+------------------+--------------------+
| Topic            | Description        |
+------------------+--------------------+
| long_gc          | Long GC Warning    |
+------------------+--------------------+
| long_schedule    | Long Schedule      |
+------------------+--------------------+
| large_heap       | Large Heap Warning |
+------------------+--------------------+
| busy_port        | Busy Port Warning  |
+------------------+--------------------+
| busy_dist_port   | Busy Dist Port     |
+------------------+--------------------+

-----
Trace
-----

The emqx broker supports to trace MQTT packets received/sent from/to a client, or trace MQTT messages published to a topic.

Trace a client::

    ./bin/emqx_ctl trace client "clientid" "trace_clientid.log"

Trace a topic::

    ./bin/emqx_ctl trace topic "topic" "trace_topic.log"

Lookup Traces::

    ./bin/emqx_ctl trace list

Stop a Trace::

    ./bin/emqx_ctl trace client "clientid" off

    ./bin/emqx_ctl trace topic "topic" off

.. _emqx_auth_clientid: https://github.com/emqx/emqx_auth_clientid
.. _emqx_auth_username: https://github.com/emqx/emqx_auth_username
.. _emqx_auth_ldap:     https://github.com/emqx/emqx_auth_ldap
.. _emqx_auth_http:     https://github.com/emqx/emqx_auth_http
.. _emqx_auth_mysql:    https://github.com/emqx/emqx_auth_mysql
.. _emqx_auth_pgsql:    https://github.com/emqx/emqx_auth_pgsql
.. _emqx_auth_redis:    https://github.com/emqx/emqx_auth_redis
.. _emqx_auth_mongo:    https://github.com/emqx/emqx_auth_mongo

