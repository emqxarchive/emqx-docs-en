
.. _getstarted:

===========
Get Started
===========

-------------------------------------------
*EMQ X* R3.1 Message Server introduction
-------------------------------------------

EMQ X (Erlang/Enterprise/Elastic MQTT Broker) is an open source IoT MQTT message server based on the Erlang/OTP platform. Erlang/OTP is an excellent soft-realtime,Low-Latency and distributed language platform. MQTT is a lightweight, publish-subscribe model (PubSub) IoT message protocol.

*EMQ X* is designed for massive **mobile/IoT/vehicle** terminal access and realizes fast and low-latency message routing between massive physical network devices:

1. Stable to host large-scale MQTT client connections, and single-server nodes support millions of connections.

2. Distributed node cluster, fast and low-latency message routing, and single-cluster supports tens of thousands of routes.

3. Extension within the message server, support customized multiple authentication methods, and efficiently store messages to the back-end database.

4. Complete IoT protocol support, including MQTT, MQTT-SN, CoAP, LwM2M, private TCP/UDP protocol.

.. _mqtt_pubsub:

--------------------------------------------
MQTT publish and subscribe mode introduction
--------------------------------------------

MQTT communication and data exchange is based on the Publish/Subscribe mode which is essentially different from the HTTP Request/Response mode.

The Subscriber subscribes to a topic from the message server (Broker). After it succeed, the message server forwards the message under the topic to all subscribers.

Topic uses '/' as a separator to distinguish between different levels. Topics that contain the wildcard '+' or '#' are also known as Topic Filters; those that do not contain wildcards are called Topic Names. For example::

    sensor/1/temperature

    chat/room/subject

    presence/user/feng

    sensor/1/#

    sensor/+/temperature

    uber/drivers/joe/inbox


.. NOTE:: '+'matches one level and'#' matches multiple levels (must be at the end).
.. NOTE:: Publishers can only post messages to 'topic names', and Subscribers can subscribe to multiple topic names by subscribing to 'topic filters'.

.. _features:

---------------------------------------
EMQ X R3.1 Message Server Features List
---------------------------------------

* Complete MQTT V3.1/V3.1.1 and V5.0 protocol specification support
* QoS0, QoS1, QoS2 message support
* Persistent session and offline message support
* Retained message support
* Last Will message support
* TCP/SSL connection support
* MQTT/WebSocket/SSL support
* HTTPmessage releasing interface support
* $SYS/#  system topic support
* Client online status query and subscription support
* Client ID or IP address authentication support
* Username password authentication support
* LDAP authentication
* Redis、MySQL、PostgreSQL、MongoDB、HTTP authentication integration
* Browser cookie authentication
* Access Control (ACL) based on client ID, IP address, and username
* Multi-server node cluster (Cluster)
* Support multiple cluster discovery methods of manual, mcast, dns, etcd, k8s and other 
* Automatic healing of network partition
* Message rate limit
* Connection rate limit
* Configuring nodes by partition
* Multi-server node bridge
* MQTT Broker bridge support
* Stomp protocol support
* MQTT-SN protocol support
* CoAP protocol support
* Stomp/SockJS support
* Publish ($delay/topic) delay
* Flapping detection
* Blacklist support
* Shared subscription($share/<group>/topic)
* TLS/PSK support
* Rule engine support

.. _quick_start:

---------------------------------------
Download and start EMQ in five minutes
---------------------------------------

Each version of EMQ X will release platform packages of CentOS, Ubuntu, Debian, FreeBSD, macOS, Windows, openSUSE  and Docker images.

Download address: https://www.emqx.io/downloads/broker?osType=Linux

Once the package is downloaded, it can be unzipped the startup directly, such as the Mac platform:


.. code-block:: bash

    unzip emqx-macosx-v3.1.0.zip && cd emqx

    # start emqx
    ./bin/emqx start

    # Check the running status
    ./bin/emqx_ctl status

    # stop emqx
    ./bin/emqx stop

After EMQ X is started, the MQTT client can access the system through port 1883. The running log output is in the directory of log/.

EMQ X loads the Dashboard plugin and launches the web management console by default. Users can view server running status, statistics, Connections, Sessions, Topics, Subscriptions, and Plugins through the web console.

Console address: http://127.0.0.1:18083，default username: admin，password:public

.. image:: ./_static/images/dashboard.png

.. _mqtt_clients:

--------------------------------
Open source MQTT client project
--------------------------------

GitHub: https://github.com/emqtt

+--------------------+------------------------------------+
| `emqttc`_          | Erlang MQTT client library         |
+--------------------+------------------------------------+
| `CocoaMQTT`_       | Swift Language MQTT Client Library |
+--------------------+------------------------------------+
| `QMQTT`_           | QT framework MQTT client library   |
+--------------------+------------------------------------+
| `emqtt_benchmark`_ | MQTT connection and test tool      |
+--------------------+------------------------------------+

Eclipse Paho: https://www.eclipse.org/paho/

MQTT.org: https://github.com/mqtt/mqtt.github.io/wiki/libraries

.. _emqttc:          https://github.com/emqtt/emqttc
.. _emqtt_benchmark: https://github.com/emqtt/emqtt_benchmark
.. _CocoaMQTT:       https://github.com/emqtt/CocoaMQTT
.. _QMQTT:           https://github.com/emqtt/qmqtt
