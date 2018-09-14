
.. _commands:

========
Commands
========

The './bin/emqx_ctl' command line could be used to query and administrate the *EMQ X* broker.

.. WARNING:: Cannot work on Windows

.. _command_status:

------
status
------

Show running status of the broker::

    $ ./bin/emqx_ctl status

    Node 'emqx@127.0.0.1' is started
    emqx 3.0 is running

.. _command_broker:

------
broker
------

Query basic information,  statistics and metrics of the broker.

+----------------+-------------------------------------------------+
| broker         | Show version, description, uptime of the broker |
+----------------+-------------------------------------------------+
| broker pubsub  | Show status of the core pubsub process          |
+----------------+-------------------------------------------------+
| broker stats   | Show statistics of client, session, topic,      |
|                | subscription and route of the broker            |
+----------------+-------------------------------------------------+
| broker metrics | Show metrics of MQTT bytes, packets, messages   |
|                | sent/received.                                  |
+----------------+-------------------------------------------------+

Query version, description and uptime of the broker::

    $ ./bin/emqx_ctl broker

    sysdescr  : EMQ X Broker
    version   : 3.0
    uptime    : 1 hours, 25 minutes, 24 seconds
    datetime  : 2018-09-13 13:17:32

broker stats
------------

Query statistics of MQTT Client, Session, Topic, Subscription and Route::

    $ ./bin/emqx_ctl broker stats

    clients/count       : 1
    clients/max         : 1
    queues/count        : 0
    queues/max          : 0
    retained/count      : 2
    retained/max        : 2
    routes/count        : 2
    routes/reverse      : 2
    sessions/count      : 0
    sessions/max        : 0
    subscriptions/count : 1
    subscriptions/max   : 1
    topics/count        : 54
    topics/max          : 54

broker metrics
--------------

Query metrics of Bytes, MQTT Packets and Messages(sent/received)::

    $ ./bin/emqx_ctl broker metrics

    bytes/received          : 297
    bytes/sent              : 40
    messages/dropped        : 348
    messages/qos0/received  : 0
    messages/qos0/sent      : 0
    messages/qos1/received  : 0
    messages/qos1/sent      : 0
    messages/qos2/received  : 0
    messages/qos2/sent      : 0
    messages/received       : 0
    messages/retained       : 2
    messages/sent           : 0
    packets/connack         : 5
    packets/connect         : 5
    packets/disconnect      : 0
    packets/pingreq         : 0
    packets/pingresp        : 0
    packets/puback/received : 0
    packets/puback/sent     : 0
    packets/pubcomp/received: 0
    packets/pubcomp/sent    : 0
    packets/publish/received: 0
    packets/publish/sent    : 0
    packets/pubrec/received : 0
    packets/pubrec/sent     : 0
    packets/pubrel/received : 0
    packets/pubrel/sent     : 0
    packets/received        : 9
    packets/sent            : 9
    packets/suback          : 4
    packets/subscribe       : 4
    packets/unsuback        : 0
    packets/unsubscribe     : 0

.. _command_cluster:

-------
cluster
-------

Cluster two or more EMQ X brokers.

+-----------------------+--------------------------------+
| cluster join <Node>   | Join the cluster               |
+-----------------------+--------------------------------+
| cluster leave         | Leave the cluster              |
+-----------------------+--------------------------------+
| cluster remove <Node> | Remove a node from the cluster |
+-----------------------+--------------------------------+
| cluster status        | Query cluster status and nodes |
+-----------------------+--------------------------------+

Suppose we create two EMQ X nodes on localhost and cluster them:

+-----------+---------------------+-------------+
| Folder    | Node                | MQTT Port   |
+-----------+---------------------+-------------+
| emqx1     | emqx1@127.0.0.1     | 1883        |
+-----------+---------------------+-------------+
| emqx2     | emqx2@127.0.0.1     | 2883        |
+-----------+---------------------+-------------+

Start emqx1 node::

    cd emqx1 && ./bin/emqx start

Start emqx2 node::

    cd emqx2 && ./bin/emqx start

Under emqx2 folder::

    $ ./bin/emqx_ctl cluster join emqx1@127.0.0.1

    Join the cluster successfully.
    Cluster status: [{running_nodes,['emqx1@127.0.0.1','emqx2@127.0.0.1']}]

Query cluster status::

    $ ./bin/emqx_ctl cluster status

    Cluster status: [{running_nodes,['emqx2@127.0.0.1','emqx1@127.0.0.1']}]

Message Route between nodes::

    # Subscribe topic 'x' on emqx1 node
    mosquitto_sub -t x -q 1 -p 1883

    # Publish to topic 'x' on emqx2 node
    mosquitto_pub -t x -q 1 -p 2883 -m hello

emqx2 leaves the cluster::

    cd emqx2 && ./bin/emqx_ctl cluster leave

Or remove emqx2 from the cluster on emqx1 node::

    cd emqx1 && ./bin/emqx_ctl cluster remove emqx2@127.0.0.1

.. _command_clients:

-------
clients
-------

Query MQTT clients connected to the broker:

+-------------------------+----------------------------------+
| clients list            | List all MQTT clients            |
+-------------------------+----------------------------------+
| clients show <ClientId> | Show an MQTT Client              |
+-------------------------+----------------------------------+
| clients kick <ClientId> | Kick out an MQTT client          |
+-------------------------+----------------------------------+

clients lists
-------------

Query All MQTT clients connected to the broker::

    $ ./bin/emqx_ctl clients list

    Client(mosqsub/43832-airlee.lo, clean_sess=true, username=test, peername=127.0.0.1:64896, connected_at=1452929113)
    Client(mosqsub/44011-airlee.lo, clean_sess=true, username=test, peername=127.0.0.1:64961, connected_at=1452929275)
    ...

Properties of the Client:

+--------------+---------------------------------------------------+
| clean_sess   | Clean Session Flag                                |
+--------------+---------------------------------------------------+
| username     | Username of the client                            |
+--------------+---------------------------------------------------+
| peername     | Peername of the TCP connection                    |
+--------------+---------------------------------------------------+
| connected_at | The timestamp when client connected to the broker |
+--------------+---------------------------------------------------+

clients show <ClientId>
-----------------------

Show a specific MQTT Client::

    ./bin/emqx_ctl clients show "mosqsub/43832-airlee.lo"

    Client(mosqsub/43832-airlee.lo, clean_sess=true, username=test, peername=127.0.0.1:64896, connected_at=1452929113)

clients kick <ClientId>
-----------------------

Kick out a MQTT Client::

    ./bin/emqx_ctl clients kick "clientid"

.. _command_sessions:

--------
sessions
--------

Query all MQTT sessions. The broker will create a session for each MQTT client. Persistent Session if clean_session flag is true, transient session otherwise.

+--------------------------+-------------------------------+
| sessions list            | List all Sessions             |
+--------------------------+-------------------------------+
| sessions list persistent | Query all persistent Sessions |
+--------------------------+-------------------------------+
| sessions list transient  | Query all transient Sessions  |
+--------------------------+-------------------------------+
| sessions show <ClientId> | Show a session                |
+--------------------------+-------------------------------+

sessions list
-------------

Query all sessions::

    $ ./bin/emqx_ctl sessions list

    Session(clientid, clean_sess=false, max_inflight=100, inflight_queue=0, message_queue=0, message_dropped=0, awaiting_rel=0, awaiting_ack=0, awaiting_comp=0, created_at=1452935508)
    Session(mosqsub/44101-airlee.lo, clean_sess=true, max_inflight=100, inflight_queue=0, message_queue=0, message_dropped=0, awaiting_rel=0, awaiting_ack=0, awaiting_comp=0, created_at=1452935401)

Properties of Session:

TODO:??

+-------------------+----------------------------------------------------------------+
| clean_sess        | clean sess flag. false: persistent, true: transient            |
+-------------------+----------------------------------------------------------------+
| max_inflight      | Inflight window (Max number of messages delivering)            |
+-------------------+----------------------------------------------------------------+
| inflight_queue    | Inflight Queue Size                                            |
+-------------------+----------------------------------------------------------------+
| message_queue     | Message Queue Size                                             |
+-------------------+----------------------------------------------------------------+
| message_dropped   | Number of Messages Dropped for queue is full                   |
+-------------------+----------------------------------------------------------------+
| awaiting_rel      | The number of QoS2 messages received and waiting for PUBREL    |
+-------------------+----------------------------------------------------------------+
| awaiting_ack      | The number of QoS1/2 messages delivered and waiting for PUBACK |
+-------------------+----------------------------------------------------------------+
| awaiting_comp     | The number of QoS2 messages delivered and waiting for PUBCOMP  |
+-------------------+----------------------------------------------------------------+
| created_at        | Timestamp when the session is created                          |
+-------------------+----------------------------------------------------------------+

sessions list persistent
------------------------

Query all persistent sessions::

    $ ./bin/emqx_ctl sessions list persistent

    Session(clientid, clean_sess=false, max_inflight=100, inflight_queue=0, message_queue=0, message_dropped=0, awaiting_rel=0, awaiting_ack=0, awaiting_comp=0, created_at=1452935508)

sessions list transient
-----------------------

Query all transient sessions::

    $ ./bin/emqx_ctl sessions list transient

    Session(mosqsub/44101-airlee.lo, clean_sess=true, max_inflight=100, inflight_queue=0, message_queue=0, message_dropped=0, awaiting_rel=0, awaiting_ack=0, awaiting_comp=0, created_at=1452935401)

sessions show <ClientId>
------------------------

Show a session::

    $ ./bin/emqx_ctl sessions show clientid

    Session(clientid, clean_sess=false, max_inflight=100, inflight_queue=0, message_queue=0, message_dropped=0, awaiting_rel=0, awaiting_ack=0, awaiting_comp=0, created_at=1452935508)

.. _command_routes:

------
routes
------

Show routing table of the broker.

routes list
-----------

List all routes::

    $ ./bin/emqx_ctl routes list

    t2/# -> emqx2@127.0.0.1
    t/+/x -> emqx2@127.0.0.1,emqx1@127.0.0.1

routes show <Topic>
-------------------

Show a route::

    $ ./bin/emqx_ctl routes show t/+/x

    t/+/x -> emqx2@127.0.0.1,emqx1@127.0.0.1

.. _command_topics:

------
topics
------

Query topic table of the broker.

topics list
-----------

Query all the topics::

    $ ./bin/emqx_ctl topics list

    $SYS/brokers/emqx1@127.0.0.1/metrics/packets/subscribe: static
    $SYS/brokers/emqx1@127.0.0.1/stats/subscriptions/max: static
    $SYS/brokers/emqx2@127.0.0.1/stats/subscriptions/count: static
    ...

topics show <Topic>
-------------------

Show a topic::

    $ ./bin/emqx_ctl topics show '$SYS/brokers'

    $SYS/brokers: static

.. _command_subscriptions:

-------------
subscriptions
-------------

Query the subscription table of the broker:

+--------------------------------------------+--------------------------------------+
| subscriptions list                         | List all subscriptions               |
+--------------------------------------------+--------------------------------------+
| subscriptions show <ClientId>              | Show a subscription                  |
+--------------------------------------------+--------------------------------------+

subscriptions list
------------------

Query all subscriptions::

    $ ./bin/emqx_ctl subscriptions list

    mosqsub/91042-airlee.lo -> t/y:1
    mosqsub/90475-airlee.lo -> t/+/x:2

subscriptions list static
-------------------------

List all static subscriptions::

    $ ./bin/emqx_ctl subscriptions list static

    clientid -> new_topic:1

subscriptions show <ClientId>
-----------------------------

Show the subscriptions of an MQTT client::

    $ ./bin/emqx_ctl subscriptions show clientid

    clientid: [{<<"x">>,1},{<<"topic2">>,1},{<<"topic3">>,1}]

.. _command_plugins:

-------
plugins
-------

List, load or unload plugins of EMQ X broker.

+---------------------------+-------------------------+
| plugins list              | List all plugins        |
+---------------------------+-------------------------+
| plugins load <Plugin>     | Load Plugin             |
+---------------------------+-------------------------+
| plugins unload <Plugin>   | Unload (Plugin)         |
+---------------------------+-------------------------+

plugins list
------------

List all plugins::

    $ ./bin/emqx_ctl plugins list

    Plugin(emqx_auth_clientid, version=3.0, description=Authentication with ClientId/Password, active=false)
    Plugin(emqx_auth_http, version=3.0, description=Authentication/ACL with HTTP API, active=false)
    Plugin(emqx_auth_ldap, version=3.0, description=Authentication/ACL with LDAP, active=false)
    Plugin(emqx_auth_mongo, version=3.0, description=Authentication/ACL with MongoDB, active=false)
    Plugin(emqx_auth_mysql, version=3.0, description=Authentication/ACL with MySQL, active=false)
    Plugin(emqx_auth_pgsql, version=3.0, description=Authentication/ACL with PostgreSQL, active=false)
    Plugin(emqx_auth_redis, version=3.0, description=Authentication/ACL with Redis, active=false)
    Plugin(emqx_auth_username, version=3.0, description=Authentication with Username/Password, active=false)
    Plugin(emqx_coap, version=3.0, description=CoAP Gateway, active=false)
    Plugin(emqx_dashboard, version=3.0, description=Dashboard, active=true)
    Plugin(emqx_mod_rewrite, version=3.0, description=EMQ Rewrite Module, active=false)
    Plugin(emqx_plugin_template, version=3.0, description=EMQ Plugin Template, active=false)
    Plugin(emqx_recon, version=3.0, description=Recon Plugin, active=false)
    Plugin(emqx_reloader, version=3.0, description=Reloader Plugin, active=false)
    Plugin(emqx_sn, version=3.0, description=MQTT-SN Gateway, active=false)
    Plugin(emqx_stomp, version=3.0, description=Stomp Protocol Plugin, active=false)

Properties of a plugin:

+-------------+--------------------------+
| version     | Plugin Version           |
+-------------+--------------------------+
| description | Plugin Description       |
+-------------+--------------------------+
| active      | If the plugin is Loaded  |
+-------------+--------------------------+

Load <Plugin>
-------------

Load a Plugin::

    $ ./bin/emqx_ctl plugins load emqx_recon

    Start apps: [recon,emqx_recon]
    Plugin emqx_recon loaded successfully.

Unload <Plugin>
---------------

Unload a Plugin::

    $ ./bin/emqx_ctl plugins unload emqx_recon

    Plugin emqx_recon unloaded successfully.

.. _command_bridges:

-------
bridges
-------

Bridge two or more *EMQ X* brokers::

                  ---------                     ---------
    Publisher --> | node1 | --Bridge Forward--> | node2 | --> Subscriber
                  ---------                     ---------

commands for bridge:

+----------------------------------------+------------------------------+
| bridges list                           | List all bridges             |
+----------------------------------------+------------------------------+
| bridges options                        | Show bridge options          |
+----------------------------------------+------------------------------+
| bridges start <Node> <Topic>           | Create a bridge              |
+----------------------------------------+------------------------------+
| bridges start <Node> <Topic> <Options> | Create a bridge with options |
+----------------------------------------+------------------------------+
| bridges stop <Node> <Topic>            | Delete a bridge              |
+----------------------------------------+------------------------------+

Suppose we create a bridge between emqx1 and emqx2 on localhost:

+---------+---------------------+-----------+
| Name    | Node                | MQTT Port |
+---------+---------------------+-----------+
| emqx1   | emqx1@127.0.0.1     | 1883      |
+---------+---------------------+-----------+
| emqx2   | emqx2@127.0.0.1     | 2883      |
+---------+---------------------+-----------+

The bridge will forward all the the 'sensor/#' messages from emqx1 to emqx2::

    $ ./bin/emqx_ctl bridges start emqx2@127.0.0.1 sensor/#

    bridge is started.

    $ ./bin/emqx_ctl bridges list

    bridge: emqx1@127.0.0.1--sensor/#-->emqx2@127.0.0.1

The the 'emqx1--sensor/#-->emqx2' bridge::

    #emqttd2 node

    mosquitto_sub -t sensor/# -p 2883 -d

    #emqttd1 node

    mosquitto_pub -t sensor/1/temperature -m "37.5" -d

bridges options
---------------

Show bridge options::

    $ ./bin/emqx_ctl bridges options

    Options:
      qos     = 0 | 1 | 2
      prefix  = string
      suffix  = string
      queue   = integer
    Example:
      qos=2,prefix=abc/,suffix=/yxz,queue=1000

bridges stop <Node> <Topic>
---------------------------

Delete the emqx1--sensor/#-->emqx2 bridge::

    $ ./bin/emqx_ctl bridges stop emqx2@127.0.0.1 sensor/#

    bridge is stopped.

.. _command_vm:

--
vm
--

Query the load, cpu, memory, processes and IO information of the Erlang VM.

+-------------+-----------------------------------+
| vm all      | Query all                         |
+-------------+-----------------------------------+
| vm load     | Query VM Load                     |
+-------------+-----------------------------------+
| vm memory   | Query Memory Usage                |
+-------------+-----------------------------------+
| vm process  | Query Number of Erlang Processes  |
+-------------+-----------------------------------+
| vm io       | Query Max Fds of VM               |
+-------------+-----------------------------------+

vm load
-------

Query load::

    $ ./bin/emqx_ctl vm load

    cpu/load1               : 2.21
    cpu/load5               : 2.60
    cpu/load15              : 2.36

vm memory
---------

Query memory::

    $ ./bin/emqx_ctl vm memory

    memory/total            : 23967736
    memory/processes        : 3594216
    memory/processes_used   : 3593112
    memory/system           : 20373520
    memory/atom             : 512601
    memory/atom_used        : 491955
    memory/binary           : 51432
    memory/code             : 13401565
    memory/ets              : 1082848

vm process
----------

Query number of erlang processes::

    $ ./bin/emqx_ctl vm process

    process/limit           : 8192
    process/count           : 221

vm io
-----

Query max, active file descriptors of IO::

    $ ./bin/emqx_ctl vm io

    io/max_fds              : 2560
    io/active_fds           : 1

.. _command_trace:

-----
trace
-----

Trace MQTT packets, messages(sent/received) by ClientId or Topic.

+-----------------------------------+-----------------------------------+
| trace list                        | List all traces                   |
+-----------------------------------+-----------------------------------+
| trace client <ClientId> <LogFile> | Trace a client                    |
+-----------------------------------+-----------------------------------+
| trace client <ClientId> off       | Stop tracing the client           |
+-----------------------------------+-----------------------------------+
| trace topic <Topic> <LogFile>     | Trace a topic                     |
+-----------------------------------+-----------------------------------+
| trace topic <Topic> off           | Stop tracing the topic            |
+-----------------------------------+-----------------------------------+

trace client <ClientId> <LogFile>
---------------------------------

Start to trace a client::

    $ ./bin/emqx_ctl trace client clientid log/clientid_trace.log

    trace client clientid successfully.

trace client <ClientId> off
---------------------------

Stop tracing the client::

    $ ./bin/emqx_ctl trace client clientid off

    stop tracing client clientid successfully.

trace topic <Topic> <LogFile>
-----------------------------

Start to trace a topic::

    $ ./bin/emqx_ctl trace topic topic log/topic_trace.log

    trace topic topic successfully.

trace topic <Topic> off
-----------------------

Stop tracing the topic::

    $ ./bin/emqx_ctl trace topic topic off

    stop tracing topic topic successfully.

trace list
----------

List all traces::

    $ ./bin/emqx_ctl trace list

    trace client clientid -> log/clientid_trace.log
    trace topic topic -> log/topic_trace.log

.. _command_listeners:

---------
listeners
---------

Show all the TCP listeners::

    $ ./bin/emqx_ctl listeners

    listener on mqtt:ws:8083
      acceptors       : 4
      max_clients     : 64
      current_clients : 0
      shutdown_count  : []
    listener on mqtt:ssl:8883
      acceptors       : 4
      max_clients     : 512
      current_clients : 0
      shutdown_count  : []
    listener on mqtt:tcp:1883
      acceptors       : 8
      max_clients     : 1024
      current_clients : 0
      shutdown_count  : []
    listener on dashboard:http:18083
      acceptors       : 2
      max_clients     : 512
      current_clients : 0
      shutdown_count  : []

listener parameters:

+-----------------+--------------------------------------+
| acceptors       | TCP Acceptor Pool                    |
+-----------------+--------------------------------------+
| max_clients     | Max number of clients                |
+-----------------+--------------------------------------+
| current_clients | Count of current clients             |
+-----------------+--------------------------------------+
| shutdown_count  | Statistics of client shutdown reason |
+----------------+---------------------------------------+

.. _command_mnesia:

------
mnesia
------

Show system_info of mnesia database.

------
admins
------

The 'admins' CLI is used to add/del admin account, which is registered by the dashboard plugin.

+------------------------------------+-----------------------------+
| admins add <Username> <Password>   | Add admin account           |
+------------------------------------+-----------------------------+
| admins passwd <Username> <Password>| Reset admin password        |
+------------------------------------+-----------------------------+
| admins del <Username>              | Delete admin account        |
+------------------------------------+-----------------------------+

admins add
----------

Add admin account::

    $ ./bin/emqx_ctl admins add root public
    ok

admins passwd
-------------

Reset password::

    $ ./bin/emqx_ctl admins passwd root private
    ok

admins del
----------

Delete admin account::

    $ ./bin/emqx_ctl admins del root
    ok
