
.. _getstarted:

===========
Get Started
===========

--------
Overview
--------

*EMQ X* (Erlang MQTT Broker) is an open source MQTT broker written in Erlang/OTP. Erlang/OTP is a concurrent, fault-tolerant, soft-realtime and distributed programming platform. MQTT is an extremely lightweight publish/subscribe messaging protocol powering IoT, M2M and Mobile applications.

The *EMQ X* project is aimed to implement a scalable, distributed, extensible open-source MQTT broker for IoT, M2M and Mobile applications that hope to handle millions of concurrent MQTT clients.

Highlights of the *EMQ X* broker:

* Full MQTT V3.1/3.1.1 & V5.0 Protocol Specifications Support
* Easy to Install - Quick Install on Linux, Mac and Windows
* Massively scalable - Scaling to 1 million connections on a single server
* Cluster and Bridge Support
* Easy to extend - Hooks and plugins to customize or extend the broker
* Pluggable Authentication - LDAP, MySQL, PostgreSQL, Redis Authentication Plugins

--------
Features
--------

* Full MQTT V3.1/V3.1.1 & V5.0 protocol specifications support
* QoS0, QoS1, QoS2 Publish and Subscribe
* Session Management and Offline Messages
* Retained Message
* Last Will Message
* TCP/SSL Connection
* MQTT Over WebSocket(SSL)
* HTTP Publish API
* STOMP protocol
* MQTT-SN Protocol
* CoAP Protocol
* STOMP over SockJS
* $SYS/# Topics
* ClientID Authentication
* IpAddress Authentication
* Username and Password Authentication
* Access control based on IpAddress, ClientID, Username
* Authentication with LDAP, Redis, MySQL, PostgreSQL and HTTP API
* Cluster brokers on several servers
* Bridge brokers locally or remotely
* mosquitto, RSMB bridge
* Extensible architecture with Hooks, Modules and Plugins
* Passed eclipse paho interoperability tests
* Local subscription
* Shared subscription

-----------
Quick Start
-----------

Download and Install
--------------------

The *EMQ X* broker is cross-platform, which could be deployed on Linux, FreeBSD, Mac, Windows and even Raspberry Pi.

Download binary package from: https://www.emqx.io/downloads.

Installing on Mac, for example:

.. code-block:: bash

    unzip emqx-macosx-v3.0.zip && cd emqx

    # Start EMQ X
    ./bin/emqx start

    # Check Status
    ./bin/emqx_ctl status

    # Stop EMQ X
    ./bin/emqx stop

Installing from Source
----------------------

.. NOTE:: The *EMQ X* broker requires Erlang/OTP R21+ to build since 3.0 release.

.. code-block:: bash

    git clone https://github.com/emqx/emqx.git

    cd emqx && make rel

    cd rel/emqx && ./bin/emqx console

-------------
Web Dashboard
-------------

A Web Dashboard will be loaded when the *EMQ X* broker is started successfully.

The Dashboard helps check running status of the broker, monitor statistics and metrics of MQTT packets, query clients, sessions, topics and subscriptions.

+------------------+---------------------------+
| Default Address  | http://localhost:18083    |
+------------------+---------------------------+
| Default User     | admin                     |
+------------------+---------------------------+
| Default Password | public                    |
+------------------+---------------------------+

.. image:: ./_static/images/dashboard.png

-------
Plugins
-------

The *EMQ X* broker could be extended by Plugins.  A plugin is an Erlang application that adds extra feature to the *EMQ X* broker:

+-------------------------+--------------------------------------------+
| `emqx_retainer`_        | Store Retained Messages                    |
+-------------------------+--------------------------------------------+
| `emqx_dashboard`_       | Web Dashboard                              |
+-------------------------+--------------------------------------------+
| `emqx_auth_clientid`_   | Authentication with ClientId               |
+-------------------------+--------------------------------------------+
| `emqx_auth_username`_   | Authentication with Username and Password  |
+-------------------------+--------------------------------------------+
| `emqx_plugin_template`_ | Plugin template and demo                   |
+-------------------------+--------------------------------------------+
| `emqx_auth_ldap`_       | LDAP Auth Plugin                           |
+-------------------------+--------------------------------------------+
| `emqx_auth_http`_       | Authentication/ACL with HTTP API           |
+-------------------------+--------------------------------------------+
| `emqx_auth_mysql`_      | Authentication with MySQL                  |
+-------------------------+--------------------------------------------+
| `emqx_auth_pgsql`_      | Authentication with PostgreSQL             |
+-------------------------+--------------------------------------------+
| `emqx_auth_redis`_      | Authentication with Redis                  |
+-------------------------+--------------------------------------------+
| `emqx_auth_mongo`_      | Authentication with MongoDB                |
+-------------------------+--------------------------------------------+
| `emqx_sn`_              | MQTT-SN Protocol Plugin                    |
+-------------------------+--------------------------------------------+
| `emqx_coap`_            | CoAP Protocol Plugin                       |
+-------------------------+--------------------------------------------+
| `emqx_stomp`_           | STOMP Protocol Plugin                      |
+-------------------------+--------------------------------------------+
| `emqx_recon`_           | Recon Plugin                               |
+-------------------------+--------------------------------------------+
| `emqx_reloader`_        | Reloader Plugin                            |
+-------------------------+--------------------------------------------+
| `emqx_web_hook`_        | Web Hook Plugin                            |
+-------------------------+--------------------------------------------+
| `emqx_lua_hook`_        | Lua Hook Plugin                            |
+-------------------------+--------------------------------------------+

A plugin could be enabled by 'bin/emqx_ctl plugins load' command.

For example, enable 'emqx_auth_pgsql' plugin::

    ./bin/emqx_ctl plugins load emqx_auth_pgsql

-----------------------
One Million Connections
-----------------------

Latest release of the *EMQ X* broker is scalable to 1.3 million MQTT connections on a 12 Core, 32G CentOS server.

.. NOTE::

    The *EMQ X* broker only allows 512 concurrent connections by default, for that 'ulimit -n' limitation is set to 1024 on most platform.

We need to tune the OS Kernel, TCP Stack, Erlang VM and *EMQ X* broker for one million connections benchmark.

Linux Kernel Parameters
-----------------------

.. code-block:: bash

    # 2M:
    sysctl -w fs.file-max=2097152
    sysctl -w fs.nr_open=2097152
    echo 2097152 > /proc/sys/fs/nr_open

    # 1M:
    ulimit -n 1048576

TCP Stack Parameters
--------------------

.. code-block:: bash

    # backlog
    sysctl -w net.core.somaxconn=65536

Erlang VM
---------

emqx/etc/emqx.conf:

.. code-block:: properties

    ## Erlang Process Limit
    node.process_limit = 2097152

    ## Sets the maximum number of simultaneously existing ports for this system
    node.max_ports = 1048576

Max Allowed Connections
-----------------------

emqx/etc/emqx.conf 'listeners':

.. code-block:: properties

    ## Size of acceptor pool
    listener.tcp.acceptors = 64

    ## Maximum number of concurrent clients
    listener.tcp.max_clients = 1000000

Test Client
-----------

.. code-block:: bash

    sysctl -w net.ipv4.ip_local_port_range="500 65535"
    echo 1000000 > /proc/sys/fs/nr_open
    ulimit -n 100000

---------------------
MQTT Client Libraries
---------------------

GitHub: https://github.com/emqtt

+--------------------+----------------------+
| `emqttc`_          | Erlang MQTT Client   |
+--------------------+----------------------+
| `emqtt_benchmark`_ | MQTT benchmark Tool  |
+--------------------+----------------------+
| `CocoaMQTT`_       | Swift MQTT Client    |
+--------------------+----------------------+
| `QMQTT`_           | QT MQTT Client       |
+--------------------+----------------------+

Eclipse Paho: https://www.eclipse.org/paho/

MQTT.org: https://github.com/mqtt/mqtt.github.io/wiki/libraries

.. _emqttc:          https://github.com/emqtt/emqttc
.. _emqtt_benchmark: https://github.com/emqtt/emqtt_benchmark
.. _CocoaMQTT:       https://github.com/emqtt/CocoaMQTT
.. _QMQTT:           https://github.com/emqtt/qmqtt

.. _emqx_plugin_template:  https://github.com/emqx/emqx-plugin-template
.. _emqx_dashboard:        https://github.com/emqx/emqx-dashboard
.. _emqx_retainer:         https://github.com/emqx/emqx-retainer
.. _emqx_auth_clientid:    https://github.com/emqx/emqx-auth-clientid
.. _emqx_auth_username:    https://github.com/emqx/emqx-auth-username
.. _emqx_auth_ldap:        https://github.com/emqx/emqx-auth-ldap
.. _emqx_auth_http:        https://github.com/emqx/emqx-auth-http
.. _emqx_auth_mysql:       https://github.com/emqx/emqx-auth-mysql
.. _emqx_auth_pgsql:       https://github.com/emqx/emqx-auth-pgsql
.. _emqx_auth_redis:       https://github.com/emqx/emqx-auth-redis
.. _emqx_auth_mongo:       https://github.com/emqx/emqx-auth-mongo
.. _emqx_reloader:         https://github.com/emqx/emqx-reloader
.. _emqx_stomp:            https://github.com/emqx/emqx-stomp
.. _emqx_recon:            https://github.com/emqx/emqx-recon
.. _emqx_sn:               https://github.com/emqx/emqx-sn
.. _emqx_coap:             https://github.com/emqx/emqx-coap
.. _emqx_web_hook:         https://github.com/emqx/emqx-web-hook
.. _emqx_lua_hook:         https://github.com/emqx/emqx-lua-hook
