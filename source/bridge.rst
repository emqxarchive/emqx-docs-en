
.. _bridge:

======
Bridge
======

-----------------
EMQ X Node Bridge
-----------------

Two or more *EMQ X* brokers can be bridged together. Bridges forward MQTT messages from one broker node to another::

                  ---------                     ---------                     ---------
    Publisher --> | Node1 | --Bridge Forward--> | Node2 | --Bridge Forward--> | Node3 | --> Subscriber
                  ---------                     ---------                     ---------

Configure Bridge
----------------

Suppose that we create two *EMQ* brokers on localhost:

+---------+---------------------+-----------+
| Name    | Node                | MQTT Port |
+---------+---------------------+-----------+
| emqx1   | emqx1@127.0.0.1     | 1883      |
+---------+---------------------+-----------+
| emqx2   | emqx2@127.0.0.1     | 2883      |
+---------+---------------------+-----------+

Create a bridge that forwards all the 'sensor/#' messages from emqx1 to emqx2.

1. Start Brokers
................

.. code-block:: bash

    cd emqx1/ && ./bin/emqx start
    cd emqx2/ && ./bin/emqx start

2. Create bridge: emqx1--sensor/#-->emqx2
.............................................

.. code-block:: bash

    $ cd emqx1 && ./bin/emqx_ctl bridges start emqx2@127.0.0.1 sensor/#

    Check if bridge is started.

    $ ./bin/emqx_ctl bridges list

    bridge: emqx1@127.0.0.1--sensor/#-->emqx2@127.0.0.1

3. Test the bridge
...................

.. code-block:: bash

    #emqx2
    mosquitto_sub -t sensor/# -p 2883 -d

    #emqx1
    mosquitto_pub -t sensor/1/temperature -m "37.5" -d

4. Delete the bridge
.....................

.. code-block:: bash

    ./bin/emqx_ctl bridges stop emqx2@127.0.0.1 sensor/#

--------------
EMQ Bridge CLI
--------------

.. code-block:: bash

    #query bridges
    ./bin/emqx_ctl bridges list

    #start bridge
    ./bin/emqx_ctl bridges start <Node> <Topic>

    #start bridge with options
    ./bin/emqx_ctl bridges start <Node> <Topic> <Options>

    #stop bridge
    ./bin/emqx_ctl bridges stop <Node> <Topic>

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

-----------
RSMB Bridge
-----------

Bridge RSMB to EMQ broker, the configuration is similar to mosquitto's.

broker.cfg::

    connection emqx
    addresses 127.0.0.1:2883
    topic sensor/#
