
.. _bridge:

===========
Bridge Node
===========

.. _bridge_emqx:

-----------------
EMQ X Node Bridge
-----------------

EMQ X support two ways to bridge different nodes, rpc and mqtt. However, RPC
bridge just support forwarding message, it doesn't support to subscribe topics
of remote nodes to sync messages. Instead, MQTT bridge both supports message
synchronize from remote nodes and forwarding messages from local node.

Bridging nodes is different from cluster, it is only charge of forwarding
messages on the basis of bridge rule. It won't copy the topic tree and routers.

Two or more *EMQ X* brokers can be bridged together. Bridges forward MQTT messages from one broker node to another::

                  ---------                     ---------                     ---------
    Publisher --> | Node1 | --Bridge Forward--> | Node2 | --Bridge Forward--> | Node3 | --> Subscriber
                  ---------                     ---------                     ---------

Configure Bridge
-----------------

Suppose that we create two *EMQ X* brokers on localhost and create a bridge to
forward messages of all sensors from emqx1 to emqx2.

+---------+---------------------+-----------+
| Name    | Node                | MQTT Port |
+---------+---------------------+-----------+
| emqx1   | emqx1@127.0.0.1     | 1883      |
+---------+---------------------+-----------+
| emqx2   | emqx2@127.0.0.1     | 2883      |
+---------+---------------------+-----------+

If the messages matched topic "sensor/#" are forwarded from node emqx1 to node
emqx2. The bridge can be configured as bellow::

    ## Bridge address: node name for local bridge, host:port for remote.
    ##
    ## Value: String
    ## Example: emqx@127.0.0.1,  127.0.0.1:1883
    bridge.emqx.address = 127.0.0.1:2883

    ## Mountpoint of the bridge.
    ##
    ## Value: String
    bridge.emqx.mountpoint = bridge/emqx/${node}/

    ## Forward message topics
    ##
    ## Value: String
    ## Example: topic1/#,topic2/#
    bridge.emqx.forwards = sensor/#

If the bridge is rpc bridge, the configuration entry: bridge.emqx.address = 127.0.0.1
should be modified as emqx2@127.0.0.1

EMQ X  MQTT bridge
-----------------------------

Some config entries can only take effect on mqtt bridge. For example::

    ## Protocol version of the bridge.
    ##
    ## Value: Enum
    ## - mqttv5
    ## - mqttv4
    ## - mqttv3
    bridge.emqx.proto_ver = mqttv4

    ## The ClientId of a remote bridge.
    ##
    ## Value: String
    bridge.emqx.client_id = bridge_emq

    ## The Clean start flag of a remote bridge.
    ##
    ## Value: boolean
    ## Default: true
    ##
    ## NOTE: Some IoT platforms require clean_start
    ##       must be set to 'true'
    bridge.emqx.clean_start = true

    ## The username for a remote bridge.
    ##
    ## Value: String
    bridge.emqx.username = user

    ## The password for a remote bridge.
    ##
    ## Value: String
    bridge.emqx.password = passwd

    ## Mountpoint of the bridge.
    ##
    ## Value: String
    bridge.emqx.mountpoint = bridge/aws/${node}/

    ## Forward message topics
    ##
    ## Value: String
    ## Example: topic1/#,topic2/#
    bridge.emqx.forwards = sensor1/#,sensor2/#

    ## Bribge to remote server via SSL.
    ##
    ## Value: on | off
    bridge.emqx.ssl = off

    ## PEM-encoded CA certificates of the bridge.
    ##
    ## Value: File
    bridge.emqx.cacertfile = {{ platform_etc_dir }}/certs/cacert.pem

    ## Client SSL Certfile of the bridge.
    ##
    ## Value: File
    bridge.emqx.certfile = {{ platform_etc_dir }}/certs/client-cert.pem

    ## Client SSL Keyfile of the bridge.
    ##
    ## Value: File
    bridge.emqx.keyfile = {{ platform_etc_dir }}/certs/client-key.pem

    ## SSL Ciphers used by the bridge.
    ##
    ## Value: String
    bridge.emqx.ciphers = ECDHE-ECDSA-AES256-GCM-SHA384,ECDHE-RSA-AES256-GCM-SHA384

    ## Ciphers for TLS PSK.
    ## Note that 'listener.ssl.external.ciphers' and 'listener.ssl.external.psk_ciphers' cannot
    ## be configured at the same time.
    ## See 'https://tools.ietf.org/html/rfc4279#section-2'.
    bridge.emqx.psk_ciphers = PSK-AES128-CBC-SHA,PSK-AES256-CBC-SHA,PSK-3DES-EDE-CBC-SHA,PSK-RC4-SHA

    ## Ping interval of a down bridge.
    ##
    ## Value: Duration
    ## Default: 10 seconds
    bridge.emqx.keepalive = 60s

    ## TLS versions used by the bridge.
    ##
    ## Value: String
    bridge.emqx.tls_versions = tlsv1.2,tlsv1.1,tlsv1

    ## Subscriptions of the bridge topic.
    ##
    ## Value: String
    bridge.emqx.subscription.1.topic = cmd/topic1

    ## Subscriptions of the bridge qos.
    ##
    ## Value: Number
    bridge.emqx.subscription.1.qos = 1

    ## Subscriptions of the bridge topic.
    ##
    ## Value: String
    bridge.emqx.subscription.2.topic = cmd/topic2

    ## Subscriptions of the bridge qos.
    ##
    ## Value: Number
    bridge.emqx.subscription.2.qos = 1

    ## Start type of the bridge.
    ##
    ## Value: enum
    ## manual
    ## auto
    bridge.emqx.start_type = manual

    ## Bridge reconnect time.
    ##
    ## Value: Duration
    ## Default: 30 seconds
    bridge.emqx.reconnect_interval = 30s

    ## Retry interval for bridge QoS1 message delivering.
    ##
    ## Value: Duration
    bridge.emqx.retry_interval = 20s

    ## Inflight size.
    ##
    ## Value: Integer
    bridge.emqx.max_inflight_batches = 32

    ## Max number of messages to collect in a batch for
    ## each send call towards emqx_bridge_connect
    ##
    ## Value: Integer
    ## default: 32
    bridge.emqx.queue.batch_count_limit = 32

    ## Max number of bytes to collect in a batch for each
    ## send call towards emqx_bridge_connect
    ##
    ## Value: Bytesize
    ## default: 1000M
    bridge.emqx.queue.batch_bytes_limit = 1000MB

    ## Base directory for replayq to store messages on disk
    ## If this config entry is missing or set to undefined,
    ## replayq works in a mem-only manner.
    ##
    ## Value: String
    bridge.emqx.queue.replayq_dir = {{ platform_data_dir }}/emqx_emqx_bridge/

    ## Replayq segment size
    ##
    ## Value: Bytesize
    bridge.emqx.queue.replayq_seg_bytes = 10MB

MQTT bridge is more flexible than RPC bridge. The config entries above are used
by MQTT connection. Not only the messages could be synchronized from remote node
to local node via mqtt bridge, but also the messages could be persisted on disk
when the mqtt bridge is broken. And when the bridge is resumed, the messages
would be published to remote nodes again. The config entry related to message
cache is beginning with `bridge.$(Bridgename).queue`. And the config entry related
to sync remote topic is beginning with `bridge.$(Bridgename).subscription`.

User could also control the bridge via cli except config file.

.. code-block:: bash

    $ cd emqx1/ && ./bin/emqx_ctl bridges
    bridges list                                    # List bridges
    bridges start <Name>                            # Start a bridge
    bridges stop <Name>                             # Stop a bridge
    bridges forwards <Name>                         # Show a bridge forward topic
    bridges add-forward <Name> <Topic>              # Add bridge forward topic
    bridges del-forward <Name> <Topic>              # Delete bridge forward topic
    bridges subscriptions <Name>                    # Show a bridge subscriptions topic
    bridges add-subscription <Name> <Topic> <Qos>   # Add bridge subscriptions topic

list bridges

.. code-block:: bash

    $ ./bin/emqx_ctl bridges list
    name: emqx     status: Stopped

Start specified bridge

.. code-block:: bash

    $ ./bin/emqx_ctl bridges start emqx
    Start bridge successfully.

Stop specified bridge

.. code-block:: bash

    $ ./bin/emqx_ctl bridges stop emqx
    Stop bridge successfully.

List forwarded topics of specified bridge

.. code-block:: bash

    $ ./bin/emqx_ctl bridges forwards emqx
    topic:   topic1/#
    topic:   topic2/#

Add forwarded topic of specified bridge

.. code-block:: bash

    $ ./bin/emqx_ctl bridges add-forwards emqx topic3/#
    Add-forward topic successfully.

Delete forwared topic for specifed bridge

.. code-block:: bash

    $ ./bin/emqx_ctl bridges del-forwards emqx topic3/#
    Del-forward topic successfully.

List subscriptions of specified bridge

.. code-block:: bash

    $ ./bin/emqx_ctl bridges subscriptions emqx
    topic: cmd/topic1, qos: 1
    topic: cmd/topic2, qos: 1

Add subscription of specified bridge

.. code-block:: bash

    $ ./_rel/emqx/bin/emqx_ctl bridges add-subscription emqx cmd/topic3 1
    Add-subscription topic successfully.

Delete subscribed topic of specified bridge

.. code-block:: bash

    $ ./_rel/emqx/bin/emqx_ctl bridges del-subscription aws cmd/topic3
    Del-subscription topic successfully.

.. _bridge_mosquitto:    

-----------------
mosquitto Bridge
-----------------

Bridge mosquitto to emqx broker::

                 -------------             -----------------
    Sensor ----> | mosquitto | --Bridge--> |               |
                 -------------             |     EMQ X     |
                 -------------             |    Cluster    |
    Sensor ----> | mosquitto | --Bridge--> |               |
                 -------------             -----------------

mosquitto.conf
--------------

Suppose that we start an emqx broker on localost:2883, and mosquitto on localhost:1883.

A bridge configured in mosquitto.conf::

    connection emqx
    address 127.0.0.1:2883
    topic sensor/# out 2

    # Set the version of the MQTT protocol to use with for this bridge. Can be one
    # of mqttv31 or mqttv311. Default is mqttv31.
    bridge_protocol_version mqttv311

.. _bridge_rsmb:

-----------
RSMB Bridge
-----------

Bridge RSMB to *EMQ X* broker, the configuration is similar to mosquitto's.

broker.cfg::

    connection emqx
    addresses 127.0.0.1:2883
    topic sensor/#
