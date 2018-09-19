
.. _plugins:

=======
Plugins
=======

The *EMQX* broker could be extended by plugins. Users could develop plugins to customize authentication, ACL and functions of the broker, or integrate the broker with other systems.

The plugins that *EMQX* 3.0 released:

+------------------------+-------------------------------+
| Plugin                 | Description                   |
+========================+===============================+
| `emqx_dashboard`_      | Web Dashboard                 |
+------------------------+-------------------------------+
| `emqx_retainer`_       | Store Retained Messages       |
+------------------------+-------------------------------+
| `emqx_auth_clientid`_  | ClientId Auth Plugin          |
+------------------------+-------------------------------+
| `emqx_auth_username`_  | Username/Password Auth Plugin |
+------------------------+-------------------------------+
| `emqx_auth_ldap`_      | LDAP Auth                     |
+------------------------+-------------------------------+
| `emqx_auth_http`_      | HTTP Auth/ACL Plugin          |
+------------------------+-------------------------------+
| `emqx_auth_mysql`_     | MySQL Auth/ACL Plugin         |
+------------------------+-------------------------------+
| `emqx_auth_pgsql`_     | PostgreSQL Auth/ACL Plugin    |
+------------------------+-------------------------------+
| `emqx_auth_redis`_     | Redis Auth/ACL Plugin         |
+------------------------+-------------------------------+
| `emqx_auth_mongo`_     | MongoDB Auth/ACL Plugin       |
+------------------------+-------------------------------+
| `emqx_web_hook`_       | Web Hook Plugin               |
++-----------------------+-------------------------------+
| `emqx_lua_hook`_       | Lua Hook Plugin               |
++-----------------------+-------------------------------+
| `emqx_coap`_           | CoAP Protocol Plugin          |
+------------------------+-------------------------------+
| `emqx_sn`_             | MQTT-SN Protocol Plugin       |
+------------------------+-------------------------------+
| `emqx_stomp`_          | STOMP Protocol Plugin         |
+------------------------+-------------------------------+
| `emqx_recon`_          | Recon Plugin                  |
+------------------------+-------------------------------+
| `emqx_reloader`_       | Reloader Plugin               |
+------------------------+-------------------------------+
| `emqx_plugin_template`_| Template Plugin               |
+------------------------+-------------------------------+

-------------------------------------
emqx_plugin_template - Template Plugin
-------------------------------------

A plugin is just a normal Erlang application which has its own configuration file: 'etc/<PluginName>.conf|config'.

emqx_plugin_template is a plugin template.

Load, unload Plugin
-------------------

Use 'bin/emqx_ctl plugins' CLI to load, unload a plugin::

    ./bin/emqx_ctl plugins load <PluginName>

    ./bin/emqx_ctl plugins unload <PluginName>

    ./bin/emqx_ctl plugins list

------------------------------
emqx_retainer - Retainer Plugin
------------------------------

Renamed the `emq_mod_retainer` to `emqx_retainer`_ project in 3.0-beta release.

Configure Retainer Plugin
-------------------------

etc/plugins/emqx_retainer.conf:

.. code-block:: properties

    ## disc: disc_copies, ram: ram_copies
    ## Notice: retainer's storage_type on each node in a cluster must be the same!
    retainer.storage_type = disc

    ## Max number of retained messages
    retainer.max_message_num = 1000000

    ## Max Payload Size of retained message
    retainer.max_payload_size = 64KB

    ## Expiry interval. Never expired if 0
    ## h - hour
    ## m - minute
    ## s - second
    retainer.expiry_interval = 0

----------------------------------------
emqx_auth_clientid - ClientID Auth Plugin
----------------------------------------

Released in 2.0-rc.2: https://github.com/emqx/emqx-auth-clientid

Configure ClientID Auth Plugin
------------------------------

etc/plugins/emqx_auth_clientid.conf:

.. code-block:: properties

    ##auth.client.$N.clientid = clientid
    ##auth.client.$N.password = passwd

    ## Examples
    ##auth.client.1.clientid = id
    ##auth.client.1.password = passwd
    ##auth.client.2.clientid = dev:devid
    ##auth.client.2.password = passwd2
    ##auth.client.3.clientid = app:appid
    ##auth.client.3.password = passwd3

Load ClientId Auth Plugin
-------------------------

.. code-block:: bash

    ./bin/emqx_ctl plugins load emqx_auth_clientid

----------------------------------------
emqx_auth_username - Username Auth Plugin
----------------------------------------

Released in 2.0-rc.2: https://github.com/emqx/emqx-auth-username

Configure Username Auth Plugin
------------------------------

etc/plugins/emqx_auth_username.conf:

.. code-block:: properties

    ##auth.user.$N.username = admin
    ##auth.user.$N.password = public

    ## Examples:
    ##auth.user.1.username = admin
    ##auth.user.1.password = public
    ##auth.user.2.username = feng@emqtt.io
    ##auth.user.2.password = public

Add username/password by `./bin/emqx_ctl users` CLI:

.. code-block:: bash

   $ ./bin/emqx_ctl users add <Username> <Password>

Load Username Auth Plugin
-------------------------

.. code-block:: bash

    ./bin/emqx_ctl plugins load emqx_auth_username

--------------------------------
emqx_dashboard - Dashboard Plugin
--------------------------------

The Web Dashboard for *EMQ* broker. The plugin will be loaded automatically when the broker started successfully.

+------------------+---------------------------+
| Address          | http://localhost:18083    |
+------------------+---------------------------+
| Default User     | admin                     |
+------------------+---------------------------+
| Default Password | public                    |
+------------------+---------------------------+

.. image:: _static/images/dashboard.png

Configure Dashboard Plugin
--------------------------

etc/plugins/emqx_dashboard.conf:

.. code-block:: properties

    ## HTTP Listener
    dashboard.listener.http = 18083
    dashboard.listener.http.acceptors = 2
    dashboard.listener.http.max_clients = 512

    ## HTTPS Listener
    ## dashboard.listener.https = 18084
    ## dashboard.listener.https.acceptors = 2
    ## dashboard.listener.https.max_clients = 512
    ## dashboard.listener.https.handshake_timeout = 15s
    ## dashboard.listener.https.certfile = etc/certs/cert.pem
    ## dashboard.listener.https.keyfile = etc/certs/key.pem
    ## dashboard.listener.https.cacertfile = etc/certs/cacert.pem
    ## dashboard.listener.https.verify = verify_peer
    ## dashboard.listener.https.fail_if_no_peer_cert = true

-------------------------------
emqx_auth_ldap: LDAP Auth Plugin
-------------------------------

LDAP Auth Plugin: https://github.com/emqx/emqx-auth-ldap

.. NOTE:: Released in 2.0-beta.1

Configure LDAP Plugin
---------------------

etc/plugins/emqx_auth_ldap.conf:

.. code-block:: properties

    auth.ldap.servers = 127.0.0.1

    auth.ldap.port = 389

    auth.ldap.timeout = 30

    auth.ldap.user_dn = uid=%u,ou=People,dc=example,dc=com

    auth.ldap.ssl = false

Load LDAP Plugin
----------------

./bin/emqx_ctl plugins load emqx_auth_ldap

------------------------------------
emqx_auth_http - HTTP Auth/ACL Plugin
------------------------------------

MQTT Authentication/ACL with HTTP API: https://github.com/emqx/emqx-auth-http

.. NOTE:: Supported in 1.1 release

Configure HTTP Auth/ACL Plugin
------------------------------

etc/plugins/emqx_auth_http.conf:

.. code-block:: properties

    ## Variables: %u = username, %c = clientid, %a = ipaddress, %P = password, %t = topic

    auth.http.auth_req = http://127.0.0.1:8080/mqtt/auth
    auth.http.auth_req.method = post
    auth.http.auth_req.params = clientid=%c,username=%u,password=%P

    auth.http.super_req = http://127.0.0.1:8080/mqtt/superuser
    auth.http.super_req.method = post
    auth.http.super_req.params = clientid=%c,username=%u

    ## 'access' parameter: sub = 1, pub = 2
    auth.http.acl_req = http://127.0.0.1:8080/mqtt/acl
    auth.http.acl_req.method = get
    auth.http.acl_req.params = access=%A,username=%u,clientid=%c,ipaddr=%a,topic=%t

HTTP Auth/ACL API
-----------------

Return 200 if ok

Return 4xx if unauthorized

Load HTTP Auth/ACL Plugin
-------------------------

.. code:: bash

    ./bin/emqx_ctl plugins load emqx_auth_http

--------------------------------------
emqx_auth_mysql - MySQL Auth/ACL Plugin
--------------------------------------

MQTT Authentication, ACL with MySQL database.

MQTT User Table
---------------

.. code-block:: sql

    CREATE TABLE `mqtt_user` (
      `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
      `username` varchar(100) DEFAULT NULL,
      `password` varchar(100) DEFAULT NULL,
      `salt` varchar(20) DEFAULT NULL,
      `is_superuser` tinyint(1) DEFAULT 0,
      `created` datetime DEFAULT NULL,
      PRIMARY KEY (`id`),
      UNIQUE KEY `mqtt_username` (`username`)
    ) ENGINE=MyISAM DEFAULT CHARSET=utf8;

MQTT ACL Table
--------------

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

    INSERT INTO `mqtt_acl` (`id`, `allow`, `ipaddr`, `username`, `clientid`, `access`, `topic`)
    VALUES
        (1,1,NULL,'$all',NULL,2,'#'),
        (2,0,NULL,'$all',NULL,1,'$SYS/#'),
        (3,0,NULL,'$all',NULL,1,'eq #'),
        (5,1,'127.0.0.1',NULL,NULL,2,'$SYS/#'),
        (6,1,'127.0.0.1',NULL,NULL,2,'#'),
        (7,1,NULL,'dashboard',NULL,1,'$SYS/#');

Configure MySQL Auth/ACL Plugin
-------------------------------

etc/plugins/emqx_auth_mysql.conf:

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

    ## ACL Query Command
    auth.mysql.acl_query = select allow, ipaddr, username, clientid, access, topic from mqtt_acl where ipaddr = '%a' or username = '%u' or username = '$all' or clientid = '%c'

Load MySQL Auth/ACL plugin
--------------------------

.. code-block:: bash

    ./bin/emqx_ctl plugins load emqx_auth_mysql

-------------------------------------------
emqx_auth_pgsql - PostgreSQL Auth/ACL Plugin
-------------------------------------------

MQTT Authentication/ACL with PostgreSQL Database.

Postgre MQTT User Table
-----------------------

.. code-block:: sql

    CREATE TABLE mqtt_user (
      id SERIAL primary key,
      is_superuser boolean,
      username character varying(100),
      password character varying(100),
      salt character varying(40)
    );

Postgre MQTT ACL Table
----------------------

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

Configure Postgre Auth/ACL Plugin
----------------------------------

Plugin Config: etc/plugins/emqx_auth_pgsql.conf.

Configure host, username, password and database of PostgreSQL:

.. code-block:: properties

    ## Postgre Server
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

    ## ACL Query. Comment this query, the acl will be disabled.
    auth.pgsql.acl_query = select allow, ipaddr, username, clientid, access, topic from mqtt_acl where ipaddr = '%a' or username = '%u' or username = '$all' or clientid = '%c'

Load Postgre Auth/ACL Plugin
-----------------------------

.. code-block:: bash

    ./bin/emqx_ctl plugins load emqx_auth_pgsql

--------------------------------------
emq_auth_redis - Redis Auth/ACL Plugin
--------------------------------------

MQTT Authentication, ACL with Redis: https://github.com/emqx/emqx_auth_redis

Configure Redis Auth/ACL Plugin
-------------------------------

etc/plugins/emqx_auth_redis.conf:

.. code-block:: properties

    ## Redis Server
    auth.redis.server = 127.0.0.1:6379

    ## Redis Pool Size
    auth.redis.pool = 8

    ## Redis Database
    auth.redis.database = 0

    ## Redis Password
    ## auth.redis.password =

    ## Variables: %u = username, %c = clientid

    ## Authentication Query Command
    auth.redis.auth_cmd = HGET mqtt_user:%u password

    ## Password hash: plain, md5, sha, sha256, pbkdf2
    auth.redis.password_hash = sha256

    ## Superuser Query Command
    auth.redis.super_cmd = HGET mqtt_user:%u is_superuser

    ## ACL Query Command
    auth.redis.acl_cmd = HGETALL mqtt_acl:%u

Redis User Hash
---------------

Set a 'user' hash with 'password' field, for example::

    HSET mqtt_user:<username> is_superuser 1
    HSET mqtt_user:<username> password "passwd"

Redis ACL Rule Hash
-------------------

The plugin uses a redis Hash to store ACL rules::

    HSET mqtt_acl:<username> topic1 1
    HSET mqtt_acl:<username> topic2 2
    HSET mqtt_acl:<username> topic3 3

.. NOTE:: 1: subscribe, 2: publish, 3: pubsub

Redis Subscription Hash
-----------------------

The plugin can store static subscriptions in a redis Hash::

    HSET mqtt_subs:<username> topic1 0
    HSET mqtt_subs:<username> topic2 1
    HSET mqtt_subs:<username> topic3 2

Load Redis Auth/ACL Plugin
--------------------------

.. code-block:: bash

    ./bin/emqx_ctl plugins load emqx_auth_redis

----------------------------------------
emqx_auth_mongo - MongoDB Auth/ACL Plugin
----------------------------------------

MQTT Authentication/ACL with MongoDB: https://github.com/emqx/emqx_auth_mongo

Configure MongoDB Auth/ACL Plugin
---------------------------------

etc/plugins/emqx_auth_mongo.conf:

.. code-block:: properties

    ## Mongo Server
    auth.mongo.server = 127.0.0.1:27017

    ## Mongo Pool Size
    auth.mongo.pool = 8

    ## Mongo User
    ## auth.mongo.user =

    ## Mongo Password
    ## auth.mongo.password =

    ## Mongo Database
    auth.mongo.database = mqtt

    ## auth_query
    auth.mongo.auth_query.collection = mqtt_user

    auth.mongo.auth_query.password_field = password

    auth.mongo.auth_query.password_hash = sha256

    auth.mongo.auth_query.selector = username=%u

    ## super_query
    auth.mongo.super_query.collection = mqtt_user

    auth.mongo.super_query.super_field = is_superuser

    auth.mongo.super_query.selector = username=%u

    ## acl_query
    auth.mongo.acl_query.collection = mqtt_user

    auth.mongo.acl_query.selector = username=%u

MongoDB Database
----------------

.. code-block:: console

    use mqtt
    db.createCollection("mqtt_user")
    db.createCollection("mqtt_acl")
    db.mqtt_user.ensureIndex({"username":1})

MongoDB User Collection
-----------------------

.. code-block:: json

    {
        username: "user",
        password: "password hash",
        is_superuser: boolean (true, false),
        created: "datetime"
    }

For example::

    db.mqtt_user.insert({username: "test", password: "password hash", is_superuser: false})
    db.mqtt_user:insert({username: "root", is_superuser: true})

MongoDB ACL Collection
----------------------

.. code-block:: json

    {
        username: "username",
        clientid: "clientid",
        publish: ["topic1", "topic2", ...],
        subscribe: ["subtop1", "subtop2", ...],
        pubsub: ["topic/#", "topic1", ...]
    }

For example::

    db.mqtt_acl.insert({username: "test", publish: ["t/1", "t/2"], subscribe: ["user/%u", "client/%c"]})
    db.mqtt_acl.insert({username: "admin", pubsub: ["#"]})

Load MongoDB Auth/ACL Plugin
----------------------------

.. code-block:: bash

    ./bin/emqx_ctl plugins load emqx_auth_mongo

------------------------------
emqx_coap: CoAP Protocol Plugin
------------------------------

CoAP Protocol Plugin: https://github.com/emqx/emqx-coap

Configure CoAP Plugin
---------------------

.. code-block:: properties

  coap.server = 5683

  coap.prefix.mqtt = mqtt

  coap.handler.mqtt = emq_coap_gateway

Load CoAP Protocol Plugin
-------------------------

.. code:: bash

    ./bin/emqx_ctl plugins load emqx_coap

libcoap Client
--------------

.. code:: bash

    yum install libcoap

    % coap client publish message
    coap-client -m post -e "qos=0&retain=0&message=payload&topic=hello" coap://localhost/mqtt

------------------------
emqx_sn: MQTT-SN Protocol
------------------------

MQTT-SN Protocol/Gateway Plugin.

Configure MQTT-SN Plugin
------------------------

.. NOTE:: UDP Port for MQTT-SN: 1884

etc/plugins/emqx_sn.conf:

.. code-block:: properties

    mqtt.sn.port = 1884

Load MQTT-SN Plugin
-------------------

.. code::

    ./bin/emqx_ctl plugins load emqx_sn

--------------------------
emqx_stomp - STOMP Protocol
--------------------------

Support STOMP 1.0/1.1/1.2 clients to connect to emq broker and communicate with MQTT Clients.

Configure Stomp Plugin
----------------------

etc/plugins/emqx_stomp.conf:

.. NOTE:: Default Port for STOMP Protocol: 61613

.. code-block:: properties

    stomp.default_user.login = guest

    stomp.default_user.passcode = guest

    stomp.allow_anonymous = true

    stomp.frame.max_headers = 10

    stomp.frame.max_header_length = 1024

    stomp.frame.max_body_length = 8192

    stomp.listener = 61613

    stomp.listener.acceptors = 4

    stomp.listener.max_clients = 512

Load Stomp Plugin
-----------------

.. code-block:: bash

    ./bin/emqx_ctl plugins load emqx_stomp

------------------------
emqx_recon - Recon Plugin
------------------------

The plugin loads `recon`_ library on a running *EMQ* broker. Recon libray helps debug and optimize an Erlang application.

Load Recon Plugin
-----------------

.. code-block:: bash

    ./bin/emqx_ctl plugins load emqx_recon

Recon CLI
---------

.. code-block:: bash

    ./bin/emqx_ctl recon

    recon memory                 #recon_alloc:memory/2
    recon allocated              #recon_alloc:memory(allocated_types, current|max)
    recon bin_leak               #recon:bin_leak(100)
    recon node_stats             #recon:node_stats(10, 1000)
    recon remote_load Mod        #recon:remote_load(Mod)

------------------------------
emqx_reloader - Reloader Plugin
------------------------------

Erlang Module Reloader for Development

.. NOTE:: Don't load the plugin in production!

Load `Reloader` Plugin
----------------------

.. code-block:: bash

    ./bin/emqx_ctl plugins load emqx_reloader

reload CLI
----------

.. code-block:: bash

    ./bin/emqx_ctl reload

    reload <Module>             # Reload a Module

------------------------
Plugin Development Guide
------------------------

Create a Plugin Project
-----------------------

Clone emqx_plugin_template source from github.com::

    git clone https://github.com/emqx/emqx-plugin-template.git

Create a plugin project with erlang.mk and depends on 'emqx' application, the 'Makefile'::

    PROJECT = emqx_plugin_abc
    PROJECT_DESCRIPTION = emqx abc plugin
    PROJECT_VERSION = 1.0

    BUILD_DEPS = emqx
    dep_emqttd = git https://github.com/emqx/emqx master

    COVER = true

    include erlang.mk

Template Plugin: https://github.com/emqx/emqx-plugin-template

Register Auth/ACL Modules
-------------------------

emq_auth_demo.erl - demo authentication module:

.. code-block:: erlang

    -module(emq_auth_demo).

    -behaviour(emqttd_auth_mod).

    -include_lib("emqttd/include/emqttd.hrl").

    -export([init/1, check/3, description/0]).

    init(Opts) -> {ok, Opts}.

    check(#mqtt_client{client_id = ClientId, username = Username}, Password, _Opts) ->
        io:format("Auth Demo: clientId=~p, username=~p, password=~p~n",
                  [ClientId, Username, Password]),
        ok.

    description() -> "Demo Auth Module".

emqx_acl_demo.erl - demo ACL module:

.. code-block:: erlang

    -module(emq_acl_demo).

    -include_lib("emqttd/include/emqttd.hrl").

    %% ACL callbacks
    -export([init/1, check_acl/2, reload_acl/1, description/0]).

    init(Opts) ->
        {ok, Opts}.

    check_acl({Client, PubSub, Topic}, Opts) ->
        io:format("ACL Demo: ~p ~p ~p~n", [Client, PubSub, Topic]),
        allow.

    reload_acl(_Opts) ->
        ok.

    description() -> "ACL Module Demo".

emqx_plugin_template_app.erl - Register the auth/ACL modules:

.. code-block:: erlang

    ok = emqttd_access_control:register_mod(auth, emq_auth_demo, []),
    ok = emqttd_access_control:register_mod(acl, emq_acl_demo, []),

Register Callbacks for Hooks
-----------------------------

The plugin could register callbacks for hooks. The hooks will be run by the broker when a client connected/disconnected, a topic subscribed/unsubscribed or a message published/delivered:

+------------------------+-----------------------------------------+
| Name                   | Description                             |
+------------------------+-----------------------------------------+
| client.connected       | Run when a client connected to the      |
|                        | broker successfully                     |
+------------------------+-----------------------------------------+
| client.subscribe       | Run before a client subscribes topics   |
+------------------------+-----------------------------------------+
| client.unsubscribe     | Run when a client unsubscribes topics   |
+------------------------+-----------------------------------------+
| session.subscribed     | Run after a client subscribed a topic   |
+------------------------+-----------------------------------------+
| session.unsubscribed   | Run after a client unsubscribed a topic |
+------------------------+-----------------------------------------+
| message.publish        | Run when a message is published         |
+------------------------+-----------------------------------------+
| message.delivered      | Run when a message is delivered         |
+------------------------+-----------------------------------------+
| message.acked          | Run when a message(qos1/2) is acked     |
+------------------------+-----------------------------------------+
| client.disconnected    | Run when a client is disconnnected      |
+------------------------+-----------------------------------------+

emqx_plugin_template.erl for example:

.. code-block:: erlang

    %% Called when the plugin application start
    load(Env) ->
        emqttd:hook('client.connected', fun ?MODULE:on_client_connected/3, [Env]),
        emqttd:hook('client.disconnected', fun ?MODULE:on_client_disconnected/3, [Env]),
        emqttd:hook('client.subscribe', fun ?MODULE:on_client_subscribe/4, [Env]),
        emqttd:hook('session.subscribed', fun ?MODULE:on_session_subscribed/4, [Env]),
        emqttd:hook('client.unsubscribe', fun ?MODULE:on_client_unsubscribe/4, [Env]),
        emqttd:hook('session.unsubscribed', fun ?MODULE:on_session_unsubscribed/4, [Env]),
        emqttd:hook('message.publish', fun ?MODULE:on_message_publish/2, [Env]),
        emqttd:hook('message.delivered', fun ?MODULE:on_message_delivered/4, [Env]),
        emqttd:hook('message.acked', fun ?MODULE:on_message_acked/4, [Env]).

Register CLI Modules
--------------------

emqx_cli_demo.erl:

.. code-block:: erlang

    -module(emqttd_cli_demo).

    -include_lib("emqttd/include/emqttd_cli.hrl").

    -export([cmd/1]).

    cmd(["arg1", "arg2"]) ->
        ?PRINT_MSG("ok");

    cmd(_) ->
        ?USAGE([{"cmd arg1 arg2", "cmd demo"}]).

emqx_plugin_template_app.erl - register the CLI module to *EMQX* broker:

.. code-block:: erlang

    emqx_ctl:register_cmd(cmd, {emq_cli_demo, cmd}, []).

There will be a new CLI after the plugin loaded::

    ./bin/emqx_ctl cmd arg1 arg2

Create Configuration File
-------------------------

Create `etc/${plugin_name}.conf|config` file for the plugin. The *EMQX* broker supports two type of config syntax:

1. ${plugin_name}.config with erlang syntax:

.. code-block:: erlang

    [
      {plugin_name, [
        {key, value}
      ]}
    ].

2. ${plugin_name}.conf with a general `k = v` syntax:

.. code-block:: properties

    plugin_name.key = value

Build and Release the Plugin
----------------------------

1. clone emqx-relx project:

.. code-block:: bash

    git clone https://github.com/emqx/emqx-rel.git

2. Add `DEPS` in Makefile:

.. code-block:: makefile

    DEPS += plugin_name
    dep_plugin_name = git url_of_plugin

3. Add the plugin in relx.config:

.. code-block:: erlang

    {plugin_name, load},

.. _emqx_retainer:         https://github.com/emqx/emqx-retainer
.. _emqx_dashboard:        https://github.com/emqx/emqx-dashboard
.. _emqx_auth_clientid:    https://github.com/emqx/emqx-auth-clientid
.. _emqx_auth_username:    https://github.com/emqx/emqx-auth-username
.. _emqx_auth_ldap:        https://github.com/emqx/emqx-auth-ldap
.. _emqx_auth_http:        https://github.com/emqx/emqx-auth-http
.. _emqx_auth_mysql:       https://github.com/emqx/emqx-auth-mysql
.. _emqx_auth_pgsql:       https://github.com/emqx/emqx-auth-pgsql
.. _emqx_auth_redis:       https://github.com/emqx/emqx-auth-redis
.. _emqx_auth_mongo:       https://github.com/emqx/emqx-auth-mongo
.. _emqx_web_hook:         https://github.com/emqx/emqx-web-hook
.. _emqx_lua_hook:         https://github.com/emqx/emqx-lua-hook
.. _emqx_sn:               https://github.com/emqx/emqx-sn
.. _emqx_coap:             https://github.com/emqx/emqx-coap
.. _emqx_stomp:            https://github.com/emqx/emqx-stomp
.. _emqx_recon:            https://github.com/emqx/emqx-recon
.. _emqx_reloader:         https://github.com/emqx/emqx-reloader
.. _emqx_plugin_template:  https://github.com/emqx/emqx-plugin-template
.. _recon:                http://ferd.github.io/recon/
