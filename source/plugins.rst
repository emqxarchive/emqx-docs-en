
.. _plugins:

Plugins
^^^^^^^^

Through module registration and hooks (Hooks) mechanism，EMQ X broker supports user to develop extension plugin to customize server authentication and service functions.

The official plug-ins provided by EMQ X include:：

+---------------------------+---------------------------------------+-------------------------------------+
|  Plugin                   | Configuration file                    | Description                         |
+===========================+=======================================+=====================================+
| `emqx_dashboard`_         + etc/plugins/emqx_dashbord.conf        | Web dashboard Plugin (Default)      |
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_auth_clientid`_     + etc/plugins/emqx_auth_clientid.conf   | ClientId Auth Plugin                |
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_auth_username`_     + etc/plugins/emqx_auth_username.conf   | Username/Password Auth Plugin       |
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_auth_jwt`_          + etc/plugins/emqx_auth_jwt.conf        | JWT Auth/access control             |
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_auth_ldap`_         + etc/plugins/emqx_auth_ldap.conf       | LDAP Auth/access control            |
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_auth_http`_         + etc/plugins/emqx_auth_http.conf       | HTTP Auth/access control            |
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_auth_mongo`_        + etc/plugins/emqx_auth_mongo.conf      | MongoDB Auth/access control         |
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_auth_mysql`_        + etc/plugins/emqx_auth_mysql.conf      | MySQL Auth/access control           |
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_auth_pgsql`_        + etc/plugins/emqx_auth_pgsql.conf      | PostgreSQL Auth/access control      |
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_auth_redis`_        + etc/plugins/emqx_auth_redis.conf      | Redis Auth/access control           |
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_psk_file`_          + etc/plugins/emqx_psk_file.conf        | PSK support                         |
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_web_hook`_          + etc/plugins/emqx_web_hook.conf        | Web Hook Plugin                     |
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_lua_hook`_          + etc/plugins/emqx_lua_hook.conf        | Lua Hook Plugin                     |
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_retainer`_          + etc/plugins/emqx_retainer.conf        | Retain Message storage module       |
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_rule_engine`_       + etc/plugins/emqx_rule_engine.conf     | Rule engine                         |
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_delayed_publish`_   + etc/plugins/emqx_delayed_publish.conf | Client message publish delay support|
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_coap`_              + etc/plugins/emqx_coap.conf            | CoAP protocol support               |
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_lwm2m`_             + etc/plugins/emqx_lwm2m.conf           | LwM2M protocol support              |
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_sn`_                + etc/plugins/emqx_sn.conf              | MQTT-SN protocol support            |
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_stomp`_             + etc/plugins/emqx_stomp.conf           | Stomp protocol support              |
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_recon`_             + etc/plugins/emqx_recon.conf           | Recon performance debugging         |
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_reloader`_          + etc/plugins/emqx_reloader.conf        | Reloader Code hot load plugin       |
+---------------------------+---------------------------------------+-------------------------------------+
| `emqx_plugin_template`_   + etc/plugins/emqx_plugin_template.conf | plugin develop template             |
+---------------------------+---------------------------------------+-------------------------------------+

There are four ways to load plugins:

1. Default loading
2. Start and stop plugin by command line
3. Start and stop plugin by Dashboard 
4. Start and stop plugin by calling management API

**Default loading**

If a plugin needs to be started by default when system starts, configure the plugin that needs to be started directly in data/loaded_plugins. For example, the plugins that are loaded by default are:

.. code:: erlang

    emqx_management.
    emqx_rule_engine.
    emqx_recon.
    emqx_retainer.
    emqx_dashboard.

**Start and stop plugin by command line**

During the running process, we can view the list of available plugins and start and stop a plugin by CLI command:

.. code:: bash

    ## Display a list of all available plugins
    ./bin/emqx_ctl plugins list

    ## Load a plugin
    ./bin/emqx_ctl plugins load emqx_auth_username

    ## Unload a plugin
    ./bin/emqx_ctl plugins unload emqx_auth_username

    ## Reload a plugin
    ./bin/emqx_ctl plugins reload emqx_auth_username

**Start and stop plugin by Dashboard**

If EMQ X has started Dashbord plugin( by default), you can also start and stop, or configure the plugin directly by visiting the plugin management page  ``http://localhost:18083/plugins``.

Dashboard Plugin
-----------------

`emqx_dashboard`_  is the web management console for the EMQ X broker, which is enabled by default. When EMQ X starts successfully, you can view it by visiting ``http://localhost:18083`` with the default username/password: admin/public.

 The basic information, statistics, and load status of the EMQ X broker, as well as the current client list (Connections), Sessions, Routing Table (Topics), and Subscriptions can be queried through dashboard.

.. image:: ./_static/images/dashboard.png

In addition, Dashboard provides a set of REST APIs for front-end calls by default. See ``Dashboard -> HTTP API`` for details.

Dashboard plugin settings
::::::::::::::::::::::::::

etc/plugins/emqx_dashboard.conf:

.. code:: properties

    ## Dashboard default username/password
    dashboard.default_user.login = admin
    dashboard.default_user.password = public

    ## Dashboard HTTP service Port Configuration
    dashboard.listener.http = 18083
    dashboard.listener.http.acceptors = 2
    dashboard.listener.http.max_clients = 512

    ## Dashboard HTTPS service Port Configuration
    ## dashboard.listener.https = 18084
    ## dashboard.listener.https.acceptors = 2
    ## dashboard.listener.https.max_clients = 512
    ## dashboard.listener.https.handshake_timeout = 15s
    ## dashboard.listener.https.certfile = etc/certs/cert.pem
    ## dashboard.listener.https.keyfile = etc/certs/key.pem
    ## dashboard.listener.https.cacertfile = etc/certs/cacert.pem
    ## dashboard.listener.https.verify = verify_peer
    ## dashboard.listener.https.fail_if_no_peer_cert = true

ClientID authentication plugin
-------------------------------

`emqx_auth_clientid`_ currently only supports connection authentication，which authenticates the client through ``clientid`` and ``password``. When the plugin stores the password, it encrypts the plaintext according to the configured hash algorithm.

.. important:: Starting with EMQ X 3.1, only the REST API/CLI management clientid is supported, and adding the default clientid from the configuration file is no longer supported.

ClientID authentication configuration
::::::::::::::::::::::::::::::::::::::

etc/plugins/emqx_auth_clientid.conf:

.. code:: properties

    ## Password encryption method
    ## Enumeration value: plain | md5 | sha | sha256
    auth.client.password_hash = sha256

Username authentication plugin
--------------------------------

`emqx_auth_username`_ currently only supports connection authentication，which authenticates the client through ``username`` and ``password``. When the plugin stores the password, it encrypts the plaintext according to the configured hash algorithm.

.. important:: Starting with EMQ X 3.1, only the REST API/CLI management clientid is supported, and adding the default username from the configuration file is no longer supported.

Username authentication configuration
::::::::::::::::::::::::::::::::::::::

etc/plugins/emqx_auth_username.conf:

.. code:: properties

    ## Password encryption method
    ## Enumeration value: plain | md5 | sha | sha256
    auth.user.password_hash = sha256

JWT authentication plugin
---------------------------

`emqx_auth_jwt`_  supports a `JWT`_-based way to authenticate connected clients and only supports connection authentication. It parses and verifies the legitimacy and timeliness of the Token, and  allows connection when satisfied.

JWT authentication configuration
:::::::::::::::::::::::::::::::::

etc/plugins/emqx_auth_jwt.conf:

.. code:: properties

    ## HMAC Hash Algorithm Key
    auth.jwt.secret = emqxsecret

    ## RSA or ECDSA algorithm's public key
    ## auth.jwt.pubkey = etc/certs/jwt_public_key.pem

    ## JWT Source of the string
    ## Enumeration value: username | password
    auth.jwt.from = password

LDAP authentication/access control plugin
------------------------------------------

`emqx_auth_ldap`_ Emqx_auth_ldap supports access to `LDAP`_ for connection authentication and access control.

LDAP authentication plugin configuration
:::::::::::::::::::::::::::::::::::::::::

etc/plugins/emqx_auth_ldap.conf:

.. code:: properties

    auth.ldap.servers = 127.0.0.1

    auth.ldap.port = 389

    auth.ldap.pool = 8

    auth.ldap.bind_dn = cn=root,dc=emqx,dc=io

    auth.ldap.bind_password = public

    auth.ldap.timeout = 30s

    auth.ldap.device_dn = ou=device,dc=emqx,dc=io

    auth.ldap.match_objectclass = mqttUser

    auth.ldap.username.attributetype = uid

    auth.ldap.password.attributetype = userPassword

    auth.ldap.ssl = false

    ## auth.ldap.ssl.certfile = etc/certs/cert.pem

    ## auth.ldap.ssl.keyfile = etc/certs/key.pem

    ## auth.ldap.ssl.cacertfile = etc/certs/cacert.pem

    ## auth.ldap.ssl.verify = verify_peer

    ## auth.ldap.ssl.fail_if_no_peer_cert = true


HTTP authentication/access control plugin
-------------------------------------------

`emqx_auth_http`_  implements the functionality of connection authentication and access control. It sends each request to the specified HTTP service and determines whether it has operational rights by its return value.
The plugin supports a total of three requests:

1. **auth.http.auth_req**: connection authentication
2. **auth.http.super_req**: determine if it is a superuser
3. **auth.http.acl_req**: Access Control Rights Query

Each requested parameter supports customization using the real client's Username, IP address, and so on.

.. NOTE:: %cn %dn support is added in the 3.1 version.

HTTP Authentication plugin configuration
::::::::::::::::::::::::::::::::::::::::

etc/plugins/emqx_auth_http.conf:

.. code:: properties

    ## Placeholder:
    ##  - %u: username
    ##  - %c: clientid
    ##  - %a: ipaddress
    ##  - %P: password
    ##  - %cn: common name of client TLS cert
    ##  - %dn: subject of client TLS cert
    auth.http.auth_req = http://127.0.0.1:8080/mqtt/auth

    ## HTTP method and parameter configuration for AUTH requests
    auth.http.auth_req.method = post
    auth.http.auth_req.params = clientid=%c,username=%u,password=%P

    auth.http.super_req = http://127.0.0.1:8080/mqtt/superuser
    auth.http.super_req.method = post
    auth.http.super_req.params = clientid=%c,username=%u

    ## Placeholder:
    ##  - %A: 1 | 2, 1 = sub, 2 = pub
    ##  - %u: username
    ##  - %c: clientid
    ##  - %a: ipaddress
    ##  - %t: topic
    auth.http.acl_req = http://127.0.0.1:8080/mqtt/acl
    auth.http.acl_req.method = get
    auth.http.acl_req.params = access=%A,username=%u,clientid=%c,ipaddr=%a,topic=%t

HTTP API return value processing
:::::::::::::::::::::::::::::::::

**Connection authentication**：

.. code:: bash

    ## Authentication succeeded
    HTTP Status Code: 200

    ## Ignore this certification
    HTTP Status Code: 200
    Body: ignore

    ## Authentication failed
    HTTP Status Code: Except 200

**Super user**：

.. code:: bash

    ## Confirm as super user
    HTTP Status Code: 200

    ## Non-super user
    HTTP Status Code: Except 200

**Access control**：

.. code:: bash

    ##  Allow  Publish/Subscribe：
    HTTP Status Code: 200

    ## Ignore this authenticatio:
    HTTP Status Code: 200
    Body: ignore

    ## Deny this Publish/Subscribe:
    HTTP Status Code: Except 200

MySQL Authentication/Access Control Plugin
-------------------------------------------

`emqx_auth_mysql`_  supports access to MySQL for connection authentication and access control. To implement these features, we need to create two tables in MySQL in the following format:
MQTT user table
:::::::::::

.. code:: sql

    CREATE TABLE `mqtt_user` (
      `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
      `username` varchar(100) DEFAULT NULL,
      `password` varchar(100) DEFAULT NULL,
      `salt` varchar(35) DEFAULT NULL,
      `is_superuser` tinyint(1) DEFAULT 0,
      `created` datetime DEFAULT NULL,
      PRIMARY KEY (`id`),
      UNIQUE KEY `mqtt_username` (`username`)
    ) ENGINE=MyISAM DEFAULT CHARSET=utf8;

.. NOTE:: The plugin also supports tables with custom structures, which can be realized by the query statement configuration via ``auth_query``.

MQTT Access Control Table
::::::::::::::::::::::::::

.. code:: sql

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

    INSERT INTO `mqtt_acl` (`id`, `allow`, `ipaddr`, `username`, `clientid`, `access`, `topic`)
    VALUES
        (1,1,NULL,'$all',NULL,2,'#'),
        (2,0,NULL,'$all',NULL,1,'$SYS/#'),
        (3,0,NULL,'$all',NULL,1,'eq #'),
        (5,1,'127.0.0.1',NULL,NULL,2,'$SYS/#'),
        (6,1,'127.0.0.1',NULL,NULL,2,'#'),
        (7,1,NULL,'dashboard',NULL,1,'$SYS/#');

 MySQL Authentication Plugin configuration
::::::::::::::::::::::::::::::::::::::::::

etc/plugins/emqx_auth_mysql.conf:

.. code:: properties

    ## Mysql server address
    auth.mysql.server = 127.0.0.1:3306

    ## Mysql connection pool size
    auth.mysql.pool = 8

    ## Mysql connection username
    ## auth.mysql.username =

    ## Mysql connection password
    ## auth.mysql.password =

    ## Mysql authentication user table name
    auth.mysql.database = mqtt

    ## Available placeholders:
    ##  - %u: username
    ##  - %c: clientid
    ##  - %cn: common name of client TLS cert
    ##  - %dn: subject of client TLS cert
    ## Note: This SQL needs and only needs to query the `password` field
    auth.mysql.auth_query = select password from mqtt_user where username = '%u' limit 1

    ## Password encryption method: plain, md5, sha, sha256, pbkdf2
    auth.mysql.password_hash = sha256

    ##  Query statement for super user
    auth.mysql.super_query = select is_superuser from mqtt_user where username = '%u' limit 1

    ## ACL query statement
    auth.mysql.acl_query = select allow, ipaddr, username, clientid, access, topic from mqtt_acl where ipaddr = '%a' or username = '%u' or username = '$all' or clientid = '%c'

In addition, to prevent the safety issue caused by password domain  being too simple, the plugin also supports password salting operations:

.. code:: properties

    ## Salted ciphertext format
    ## auth.mysql.password_hash = salt,sha256
    ## auth.mysql.password_hash = salt,bcrypt
    ## auth.mysql.password_hash = sha256,salt

    ## pbkdf2 with macfun format
    ## macfun: md4, md5, ripemd160, sha, sha224, sha256, sha384, sha512
    ## auth.mysql.password_hash = pbkdf2,sha256,1000,20

.. note:: %cn %dn support is added in version 3.1.

Postgres authentication plugin
-------------------------------

`emqx_auth_pgsql`_ implements connection authentication and access control by accessing Postgres. Two tables are required to be difined  as follows:

Postgres MQTT  User Table
::::::::::::::::::::::::::

.. code:: sql

    CREATE TABLE mqtt_user (
      id SERIAL primary key,
      is_superuser boolean,
      username character varying(100),
      password character varying(100),
      salt character varying(40)
    );

Postgres MQTT Access control table
:::::::::::::::::::::::::::::::::::

.. code:: sql

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

Postgres authentication plugin configuration
:::::::::::::::::::::::::::::::::::::::::::::

etc/plugins/emqx_auth_pgsql.conf:

.. code:: properties

    ## PostgreSQL Service Address
    auth.pgsql.server = 127.0.0.1:5432

    ## PostgreSQL connection pool size
    auth.pgsql.pool = 8

    auth.pgsql.username = root

    ## auth.pgsql.password =

    auth.pgsql.database = mqtt

    auth.pgsql.encoding = utf8

    ## Connection authentication query SQL
    ## Placeholder:
    ##  - %u: username
    ##  - %c: clientid
    ##  - %cn: common name of client TLS cert
    ##  - %dn: subject of client TLS cert
    auth.pgsql.auth_query = select password from mqtt_user where username = '%u' limit 1

    ## Encryption method: plain | md5 | sha | sha256 | bcrypt
    auth.pgsql.password_hash = sha256

    ## Query Statement for super user (Placeholders are consistent with authentication)
    auth.pgsql.super_query = select is_superuser from mqtt_user where username = '%u' limit 1

    ## ACL query statement
    ##
    ## Placeholder:
    ##  - %a: ipaddress
    ##  - %u: username
    ##  - %c: clientid
    auth.pgsql.acl_query = select allow, ipaddr, username, clientid, access, topic from mqtt_acl where ipaddr = '%a' or username = '%u' or username = '$all' or clientid = '%c'

The same password_hash can be configured for a more secure mode:：

.. code:: properties

    ## Salted Encryption Format
    ## auth.pgsql.password_hash = salt,sha256
    ## auth.pgsql.password_hash = sha256,salt
    ## auth.pgsql.password_hash = salt,bcrypt

    ## pbkdf2 macfun format
    ## macfun: md4, md5, ripemd160, sha, sha224, sha256, sha384, sha512
    ## auth.pgsql.password_hash = pbkdf2,sha256,1000,20

Enable the following configuration to support TLS connections to Postgres:

.. code:: properties

    ## Whether to enable SSL
    auth.pgsql.ssl = false

    ## Certificate Configuration
    ## auth.pgsql.ssl_opts.keyfile =
    ## auth.pgsql.ssl_opts.certfile =
    ## auth.pgsql.ssl_opts.cacertfile =

.. note:: %cn %dn support is added in version 3.1.

Redis Authentication/Access Control Plugin
--------------------------------------------

`emqx_auth_redis`_  implements connection authentication and access control functions by accessing Redis data.

Redis authentication plugin configuration
::::::::::::::::::::::::::::::::::::::::::

etc/plugins/emqx_auth_redis.conf:

.. code:: properties

    ## Redis Service Cluster Type
    ## enumeration value: single | sentinel | cluster
    auth.redis.type = single

    ## Redis Server Address
    ##
    ## Single Redis Server: 127.0.0.1:6379, localhost:6379
    ## Redis Sentinel: 127.0.0.1:26379,127.0.0.2:26379,127.0.0.3:26379
    ## Redis Cluster: 127.0.0.1:6379,127.0.0.2:6379,127.0.0.3:6379
    auth.redis.server = 127.0.0.1:6379

    ## Redis sentinel name
    ## auth.redis.sentinel = mymaster

    ## Redis connection pool size
    auth.redis.pool = 8

    ## Redis database Serial number
    auth.redis.database = 0

    ## Redis password.
    ## auth.redis.password =

    ## Authentication Query Command
    ## Placeholder:
    ##  - %u: username
    ##  - %c: clientid
    ##  - %cn: common name of client TLS cert
    ##  - %dn: subject of client TLS cert
    auth.redis.auth_cmd = HMGET mqtt_user:%u password

    ## Password encryption method.
    ## enumeration value: plain | md5 | sha | sha256 | bcrypt
    auth.redis.password_hash = plain

    ## Super User Query Command (Placeholder is consistent with authentication)
    auth.redis.super_cmd = HGET mqtt_user:%u is_superuser

    ## ACL query command
    ##  Placeholder:
    ##  - %u: username
    ##  - %c: clientid
    auth.redis.acl_cmd = HGETALL mqtt_acl:%u

Again, the plugin supports a more secure password format:

.. code:: properties

    ## Salted ciphertext format
    ## auth.redis.password_hash = salt,sha256
    ## auth.redis.password_hash = sha256,salt
    ## auth.redis.password_hash = salt,bcrypt

    ## pbkdf2 macfun format
    ## macfun: md4, md5, ripemd160, sha, sha224, sha256, sha384, sha512
    ## auth.redis.password_hash = pbkdf2,sha256,1000,20

.. note::  %cn %dn support is added in version 3.1.

Redis user Hash
::::::::::::::::

The default is based on user Hash authentication:

.. code::

    HSET mqtt_user:<username> is_superuser 1
    HSET mqtt_user:<username> password "passwd"
    HSET mqtt_user:<username> salt "salt"

Redis ACL rule Hash
::::::::::::::::::::

 ACL rules is stored in Hash by default.

.. code::

    HSET mqtt_acl:<username> topic1 1
    HSET mqtt_acl:<username> topic2 2
    HSET mqtt_acl:<username> topic3 3

.. NOTE:: 1: subscribe, 2: publish, 3: pubsub

MongoDB authentication/access control plugin
---------------------------------------------

`emqx_auth_mongo`_ implements connection authentication and access control by accessing MongoDB.

MongoDB authentication plugin configuration
:::::::::::::::::::::::::::::::::::::::::::

etc/plugins/emqx_auth_mongo.conf:

.. code:: properties

    ## MongoDB topology type
    ## enumeration:  single | unknown | sharded | rs
    auth.mongo.type = single

    ## `set name` under rs mode
    ## auth.mongo.rs_set_name =

    ## MongoDB Service Address
    auth.mongo.server = 127.0.0.1:27017

    ## MongoDB connection pool size
    auth.mongo.pool = 8

    ## Connection authentication information
    ## auth.mongo.login =
    ## auth.mongo.password =
    ## auth.mongo.auth_source = admin

    ## Authentication data table name
    auth.mongo.database = mqtt

    ## Authentication Query Configuration
    auth.mongo.auth_query.collection = mqtt_user
    auth.mongo.auth_query.password_field = password
    auth.mongo.auth_query.password_hash = sha256

    ## Connection Authentication Query Field List
    ## Placeholder:
    ##  - %u: username
    ##  - %c: clientid
    ##  - %cn: common name of client TLS cert
    ##  - %dn: subject of client TLS cert
    auth.mongo.auth_query.selector = username=%u

    ## Super User Query
    auth.mongo.super_query = on
    auth.mongo.super_query.collection = mqtt_user
    auth.mongo.super_query.super_field = is_superuser
    auth.mongo.super_query.selector = username=%u

    ## ACL  Query Configuration
    auth.mongo.acl_query = on
    auth.mongo.acl_query.collection = mqtt_acl

    auth.mongo.acl_query.selector = username=%u

.. note:: %cn %dn support is added in version 3.1.

MongoDB database
::::::::::::::::::

.. code:: javascript

    use mqtt
    db.createCollection("mqtt_user")
    db.createCollection("mqtt_acl")
    db.mqtt_user.ensureIndex({"username":1})

.. NOTE:: The name of database and collection can be customized.

MongoDB user collection
:::::::::::::::::::::::

.. code:: javascript

    {
        username: "user",
        password: "password hash",
        is_superuser: boolean (true, false),
        created: "datetime"
    }

Example:

.. code::

    db.mqtt_user.insert({username: "test", password: "password hash", is_superuser: false})
    db.mqtt_user:insert({username: "root", is_superuser: true})

MongoDB ACL collection
:::::::::::::::::::::::

.. code:: javascript

    {
        username: "username",
        clientid: "clientid",
        publish: ["topic1", "topic2", ...],
        subscribe: ["subtop1", "subtop2", ...],
        pubsub: ["topic/#", "topic1", ...]
    }

Example:

.. code::

    db.mqtt_acl.insert({username: "test", publish: ["t/1", "t/2"], subscribe: ["user/%u", "client/%c"]})
    db.mqtt_acl.insert({username: "admin", pubsub: ["#"]})

PSK authentication plugin
---------------------------

`emqx_psk_file`_ mainly provides PSK support that aimes to implement connection authentication through PSK when the client establishes a TLS/DTLS connection.

PSK authentication plugin configuration
:::::::::::::::::::::::::::::::::::::::

etc/plugins/emqx_psk_file.conf:

.. code:: properties

    psk.file.path = etc/psk.txt

WebHook plugin
--------------

`emqx_web_hook`_  can send all EMQ X events and messages to the specified HTTP server.

WebHook plugin configuration
:::::::::::::::::::::::::::::

etc/plugins/emqx_web_hook.conf:

.. code:: properties

    ## Callback Web Server Address
    web.hook.api.url = http://127.0.0.1:8080

    ## Message and event configuration
    web.hook.rule.client.connected.1     = {"action": "on_client_connected"}
    web.hook.rule.client.disconnected.1  = {"action": "on_client_disconnected"}
    web.hook.rule.client.subscribe.1     = {"action": "on_client_subscribe"}
    web.hook.rule.client.unsubscribe.1   = {"action": "on_client_unsubscribe"}
    web.hook.rule.session.created.1      = {"action": "on_session_created"}
    web.hook.rule.session.subscribed.1   = {"action": "on_session_subscribed"}
    web.hook.rule.session.unsubscribed.1 = {"action": "on_session_unsubscribed"}
    web.hook.rule.session.terminated.1   = {"action": "on_session_terminated"}
    web.hook.rule.message.publish.1      = {"action": "on_message_publish"}
    web.hook.rule.message.deliver.1      = {"action": "on_message_deliver"}
    web.hook.rule.message.acked.1        = {"action": "on_message_acked"}

Lua plugin
-----------

`emqx_lua_hook`_ sends all events and messages to the specified Lua function. See its README for specific use.

Retainer plugin
---------------

`emqx_retainer`_  is set to start by default and provides Retained type message support for EMQ X. It stores the Retained messages for all topics in the cluster's database and posts the message when the client subscribes to the topic

Retainer plugin configuration
:::::::::::::::::::::::::::::::

etc/plugins/emqx_retainer.conf:

.. code:: properties

    ## retained Message storage method
    ##  - ram: memory only
    ##  - disc: memory and disk
    ##  - disc_only: disk only
    retainer.storage_type = ram

    ## Maximum number of storage (0 means unrestricted)
    retainer.max_retained_messages = 0

    ## Maximum storage size for single message 
    retainer.max_payload_size = 1MB

    ## Expiration time, 0 means never expired
    ## Unit:  h hour; m minute; s second.For example, 60m means 60 minutes.
    retainer.expiry_interval = 0

Delayed Publish plugin
-----------------------

`emqx_delayed_publish`_ provides the function to delay sending messages. When the client posts a message to EMQ X using the special topic prefix ``$delayed/<seconds>/``, EMQ X will publish the topic message after <seconds> seconds. 

CoAP  protocol plugin
-----------------------

`emqx_coap`_ provides support for the CoAP protocol (RFC 7252)。

CoAP protocol plugin configuration
::::::::::::::::::::::::::::::::::

etc/plugins/emqx_coap.conf:

.. code:: properties

    coap.port = 5683

    coap.keepalive = 120s

    coap.enable_stats = off

DTLS can be supported if the following two configurations are enabled:

.. code:: properties

    coap.keyfile = etc/certs/key.pem

    coap.certfile = etc/certs/cert.pem

Test the CoAP plugin
::::::::::::::::::::

We can test EMQ X's support for the CoAP protocol by installing libcoap.

.. code:: bash

    yum install libcoap

    % coap client publish message
    coap-client -m put -e "qos=0&retain=0&message=payload&topic=hello" coap://localhost/mqtt

LwM2M protocol plugin
----------------------

`emqx_lwm2m`_ provides support for the LwM2M protocol.

LwM2M plugin configuration
::::::::::::::::::::::::::

etc/plugins/emqx_lwm2m.conf:

.. code:: properties

    ## LwM2M listening port
    lwm2m.port = 5683

    ## Lifetime Limit
    lwm2m.lifetime_min = 1s
    lwm2m.lifetime_max = 86400s

    ## `time window` length under Q Mode Mode, in seconds.
    ## Messages that exceed the window will be cached
    #lwm2m.qmode_time_window = 22

    ## Whether LwM2M is deployed after coaproxy
    #lwm2m.lb = coaproxy

    ## Actively observe all objects after the device goes online
    #lwm2m.auto_observe = off

    ## the subscribed topic from EMQ X after client register succeeded
    ## Placeholder:
    ##    '%e': Endpoint Name
    ##    '%a': IP Address
    lwm2m.topics.command = lwm2m/%e/dn/#

    ## client response message to EMQ X topic
    lwm2m.topics.response = lwm2m/%e/up/resp

    ## client noify message to EMQ X topic
    lwm2m.topics.notify = lwm2m/%e/up/notify

    ## client register message to EMQ X topic
    lwm2m.topics.register = lwm2m/%e/up/resp

    # client update message to EMQ X topic
    lwm2m.topics.update = lwm2m/%e/up/resp

    # xml file location defined by object
    lwm2m.xml_dir =  etc/lwm2m_xml

Again, DTLS support can be enabled with the following configuration:

.. code:: properties

    # DTLS Certificate Configuration
    lwm2m.certfile = etc/certs/cert.pem
    lwm2m.keyfile = etc/certs/key.pem

MQTT-SN  protocol plugin
-------------------------

`emqx_sn`_ provides support for the MQTT-SN protocol

MQTT-SN protocol plugin configuration
::::::::::::::::::::::::::::::::::::::

etc/plugins/emqx_sn.conf:

.. code:: properties

    mqtt.sn.port = 1884

Stomp protocol plugin
----------------------

`emqx_stomp`_  provides support for the Stomp protocol, and supports client to connect to EMQ X through Stomp 1.0/1.1/1.2 protocol, publish and subscribe MQTT message.

Stomp plugin configuration
::::::::::::::::::::::::::::::

.. NOTE:: Stomp protocol port: 61613

etc/plugins/emqx_stomp.conf:

.. code:: properties

    stomp.default_user.login = guest

    stomp.default_user.passcode = guest

    stomp.allow_anonymous = true

    stomp.frame.max_headers = 10

    stomp.frame.max_header_length = 1024

    stomp.frame.max_body_length = 8192

    stomp.listener = 61613

    stomp.listener.acceptors = 4

    stomp.listener.max_clients = 512

Recon Performance Debugging Plugin
-----------------------------------

`emqx_recon`_ integrates the recon performance tuning library to view status information about the current system, for example:

.. code:: bash

    ./bin/emqx_ctl recon

    recon memory                 #recon_alloc:memory/2
    recon allocated              #recon_alloc:memory(allocated_types, current|max)
    recon bin_leak               #recon:bin_leak(100)
    recon node_stats             #recon:node_stats(10, 1000)
    recon remote_load Mod        #recon:remote_load(Mod)

 Recon plugin configuration
:::::::::::::::::::::::::::

etc/plugins/emqx_recon.conf:

.. code:: properties

    %% Garbage Collection: 10 minutes
    recon.gc_interval = 600

Reloader  hot load plugin
--------------------------

`emqx_reloader`_  is used for code hot-upgrade. After loading the plug-in, EMQ X updates the code automatically according to the configuration interval.

At the same time, a CLI command is also provided to specify a module to reload:

.. code:: bash

    ./bin/emqx_ctl reload <Module>

.. NOTE:: This plugin is not recommended for product deployment environments.

Reloader plugin configuration
::::::::::::::::::::::::::::::

etc/plugins/emqx_reloader.conf:

.. code:: properties

    reloader.interval = 60

    reloader.logfile = log/reloader.log

Plugin development template
----------------------------

`emqx_plugin_template`_ is an EMQ X plugin template that doesn't make any sense in functionality.

When developers need to customize plugins, they can view the plugin's code and structure to develop a standard EMQ X plugin faster. The plugin is actually a normal ``Erlang Application`` with the configuration file: ``etc/${PluginName}.config`.

EMQ X R3.1 plugin development
-----------------------------

Create a plugin project
::::::::::::::::::::::::

Create a new plugin project with reference to the `emqx_plugin_template`_ .
.. NOTE:: The tag ``-emqx_plugin(?MODULE).`` must be added to the ``<plugin name>_app.erl`` file to indicate that this is a plugin for EMQ X.

Create an authentication/access control module
:::::::::::::::::::::::::::::::::::::::::::::::

Authentication demo module - emqx_auth_demo.erl

.. code:: erlang

    -module(emqx_auth_demo).

    -export([ init/1
            , check/2
            , description/0
            ]).

    init(Opts) -> {ok, Opts}.

    check(_Credentials = #{client_id := ClientId, username := Username, password := Password}, _State) ->
        io:format("Auth Demo: clientId=~p, username=~p, password=~p~n", [ClientId, Username, Password]),
        ok.

    description() -> "Auth Demo Module".

Access Control Demo Module - emqx_acl_demo.erl

.. code:: erlang

    -module(emqx_acl_demo).

    -include_lib("emqx/include/emqx.hrl").

    %% ACL callbacks
    -export([ init/1
            , check_acl/5
            , reload_acl/1
            , description/0
            ]).

    init(Opts) ->
        {ok, Opts}.

    check_acl({Credentials, PubSub, _NoMatchAction, Topic}, _State) ->
        io:format("ACL Demo: ~p ~p ~p~n", [Credentials, PubSub, Topic]),
        allow.

    reload_acl(_State) ->
        ok.

    description() -> "ACL Demo Module".

Registration authentication, access control module - emqx_plugin_template_app.erl

.. code:: erlang

    ok = emqx:hook('client.authenticate', fun emqx_auth_demo:check/2, []),
    ok = emqx:hook('client.check_acl', fun emqx_acl_demo:check_acl/5, []).

Hooks
:::::::

client online and offline, topic subscription, message sending and receiving can be handlled through hooks.

emqx_plugin_template.erl:

.. code:: erlang

    %% Called when the plugin application start
    load(Env) ->
        emqx:hook('client.authenticate', fun ?MODULE:on_client_authenticate/2, [Env]),
        emqx:hook('client.check_acl', fun ?MODULE:on_client_check_acl/5, [Env]),
        emqx:hook('client.connected', fun ?MODULE:on_client_connected/4, [Env]),
        emqx:hook('client.disconnected', fun ?MODULE:on_client_disconnected/3, [Env]),
        emqx:hook('client.subscribe', fun ?MODULE:on_client_subscribe/3, [Env]),
        emqx:hook('client.unsubscribe', fun ?MODULE:on_client_unsubscribe/3, [Env]),
        emqx:hook('session.created', fun ?MODULE:on_session_created/3, [Env]),
        emqx:hook('session.resumed', fun ?MODULE:on_session_resumed/3, [Env]),
        emqx:hook('session.subscribed', fun ?MODULE:on_session_subscribed/4, [Env]),
        emqx:hook('session.unsubscribed', fun ?MODULE:on_session_unsubscribed/4, [Env]),
        emqx:hook('session.terminated', fun ?MODULE:on_session_terminated/3, [Env]),
        emqx:hook('message.publish', fun ?MODULE:on_message_publish/2, [Env]),
        emqx:hook('message.deliver', fun ?MODULE:on_message_deliver/3, [Env]),
        emqx:hook('message.acked', fun ?MODULE:on_message_acked/3, [Env]),
        emqx:hook('message.dropped', fun ?MODULE:on_message_dropped/3, [Env]).

All available hooks description:

+------------------------+----------------------------------+
| Hooks                  | Description                      |
+========================+==================================+
| client.authenticate    | connection authentication        |
+------------------------+----------------------------------+
| client.check_acl       | ACL validation                   |
+------------------------+----------------------------------+
| client.connected       | client online                    |
+------------------------+----------------------------------+
| client.disconnected    | client  disconnected             |
+------------------------+----------------------------------+
| client.subscribe       | subscribe topic by client        |
+------------------------+----------------------------------+
| client.unsubscribe     | unsubscribe topic by client      |
+------------------------+----------------------------------+
| session.created        | session creation                 |
+------------------------+----------------------------------+
| session.resumed        | session recovery                 |
+------------------------+----------------------------------+
| session.subscribed     | session after topic subscribed   |
+------------------------+----------------------------------+
| session.unsubscribed   | session after topic unsubscribed |
+------------------------+----------------------------------+
| session.terminated     | session terminated               |
+------------------------+----------------------------------+
| message.publish        | MQTT message publish             |
+------------------------+----------------------------------+
| message.deliver        | MQTT message deliver             |
+------------------------+----------------------------------+
| message.acked          | MQTT  message acked              |
+------------------------+----------------------------------+
| message.dropped        | MQTT message dropped             |
+------------------------+----------------------------------+

Register CLI command
:::::::::::::::::::::

Demo module for extended command line - emqx_cli_demo.erl

.. code:: erlang

    -module(emqx_cli_demo).

    -export([cmd/1]).

    cmd(["arg1", "arg2"]) ->
        emqx_cli:print("ok");

    cmd(_) ->
        emqx_cli:usage([{"cmd arg1 arg2", "cmd demo"}]).

Register command line module - emqx_plugin_template_app.erl

.. code:: erlang

    ok = emqx_ctl:register_command(cmd, {emqx_cli_demo, cmd}, []),

After the plugin is loaded，a new command line is added by ``./bin/emqx_ctl``：

.. code:: bash

    ./bin/emqx_ctl cmd arg1 arg2

Plugin configuration file
::::::::::::::::::::::::::

The plugin comes with a configuration file placed in ``etc/${plugin_name}.conf|config``. EMQ X supports two plugin configuration formats:

1. Erlang native configuration file format - ``${plugin_name}.config``::

    [
      {plugin_name, [
        {key, value}
      ]}
    ].

2. sysctl's ``k = v`` universal forma - ``${plugin_name}.conf``::

    plugin_name.key = value

.. NOTE:: ``k = v`` ormat configuration requires the plugin developer to create a ``priv/plugin_name.schema`` mapping file.

Compile and release plugin
:::::::::::::::::::::::::::

1. clone emqx-rel project:

.. code:: bash

    git clone https://github.com/emqx/emqx-rel.git

2. Makefile adds ``DEPS``：

.. code:: makefile

    DEPS += plugin_name
    dep_plugin_name = git url_of_plugin

3. The release paragraph in relx.config is added:

.. code:: erlang

    {plugin_name, load},

.. _emqx_dashboard:        https://github.com/emqx/emqx-dashboard
.. _emqx_retainer:         https://github.com/emqx/emqx-retainer
.. _emqx_delayed_publish:  https://github.com/emqx/emqx-delayed-publish
.. _emqx_auth_clientid:    https://github.com/emqx/emqx-auth-clientid
.. _emqx_auth_username:    https://github.com/emqx/emqx-auth-username
.. _emqx_auth_ldap:        https://github.com/emqx/emqx-auth-ldap
.. _emqx_auth_http:        https://github.com/emqx/emqx-auth-http
.. _emqx_auth_mysql:       https://github.com/emqx/emqx-auth-mysql
.. _emqx_auth_pgsql:       https://github.com/emqx/emqx-auth-pgsql
.. _emqx_auth_redis:       https://github.com/emqx/emqx-auth-redis
.. _emqx_auth_mongo:       https://github.com/emqx/emqx-auth-mongo
.. _emqx_auth_jwt:         https://github.com/emqx/emqx-auth-jwt
.. _emqx_web_hook:         https://github.com/emqx/emqx-web-hook
.. _emqx_lua_hook:         https://github.com/emqx/emqx-lua-hook
.. _emqx_sn:               https://github.com/emqx/emqx-sn
.. _emqx_coap:             https://github.com/emqx/emqx-coap
.. _emqx_lwm2m:            https://github.com/emqx/emqx-lwm2m
.. _emqx_stomp:            https://github.com/emqx/emqx-stomp
.. _emqx_recon:            https://github.com/emqx/emqx-recon
.. _emqx_reloader:         https://github.com/emqx/emqx-reloader
.. _emqx_psk_file:         https://github.com/emqx/emqx-psk-file
.. _emqx_plugin_template:  https://github.com/emqx/emqx-plugin-template
.. _emqx_rule_engine:      https://github.com/emqx/emqx-rule-engine
.. _recon:                 http://ferd.github.io/recon/
.. _LDAP:                  https://ldap.com
.. _JWT:                   https://jwt.io
.. _libcoap:               https://github.com/obgm/libcoap
.. _MQTT-SN:               https://github.com/emqx/emqx-sn
