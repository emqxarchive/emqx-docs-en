
.. _mqtt_sn:

================
MQTT-SN Protocol
================

MQTT-SN stands for "MQTT for Sensor Networks" which aims at embedded devices on non-TCP/IP networks, such as ZIGBEE. Its offical site says:

MQTT-SN is a publish/subscribe messaging protocol for wireless sensor networks (WSN), with the aim of extending the MQTT protocol beyond the reach of TCP/IP infrastructure for Sensor and Actuator solutions.

MQTT-SN specification can be downloaded from http://mqtt.org/new/wp-content/uploads/2009/06/MQTT-SN_spec_v1.2.pdf.
    
---------------
MQTT-SN vs MQTT
---------------

MQTT-SN looks similar to MQTT in most parts, such as Will message, such as Connect/Subscribe/Publish command.

The very difference between MQTT-SN and MQTT is the TopicId which replaces topic name in MQTT. TopicId is a 16 bits integer which stands for a topic name. Device and broker use REGISTER command to negotiate the mapping bewteen TopicId and topic name.

MQTT-SN is able to update Will message, even delete it. But MQTT is not allowed to change Will which is set in CONNECT command only.

MQTT-SN introduces gateways in its network. Gateway translates between MQTT-SN and MQTT, exchanges messages between device and MQTT broker. And there is a mechanism that called gateway discovery, which enables device to find gateways automatically.

MQTT-SN supports sleeping client feature which allows device to shutdown itself to save power for a while. Gateway needs to buffer downlink publish messages for sleeping devices, and pushes these messages to devices once they are awake.

--------------
EMQX-SN Plugin
--------------

EMQX-SN is an EMQ X plugin which implements most features of MQTT-SN. It serves as an MQTT-SN gateway on cloud, who is the neighbor of EMQ X Broker.

Plugin config
-------------

File: etc/plugins/emqx_sn.conf:

.. code-block:: properties

    mqtt.sn.port = 1884
    
    mqtt.sn.advertise_duration = 900
    
    mqtt.sn.gateway_id = 1
    
    mqtt.sn.username = mqtt_sn_user
    
    mqtt.sn.password = abc

+-----------------------------+--------------------------------------------------------------------------+
| mqtt.sn.port                | The UDP port which emqx-sn is listening on.                              |
+-----------------------------+--------------------------------------------------------------------------+
| mqtt.sn.advertise_duration  | The duration(seconds) that emqx-sn broadcasts ADVERTISE message through. |
+-----------------------------+--------------------------------------------------------------------------+
| mqtt.sn.gateway_id          | Gateway id in ADVERTISE message.                                         |
+-----------------------------+--------------------------------------------------------------------------+
| mqtt.sn.username            | This parameter is optional. If specified, emqx-sn will connect EMQ X      |
|                             | Broker with this username. It is useful if any auth plug-in is enabled.  |
+-----------------------------+--------------------------------------------------------------------------+
| mqtt.sn.password            | This parameter is optional. Pair with username above.                    |
+-----------------------------+--------------------------------------------------------------------------+

Load Plugin
-----------

.. code-block:: console

    ./bin/emqx_ctl plugins load emqx_sn

----------------------
MQTT-SN Client Library
----------------------

1. https://github.com/eclipse/paho.mqtt-sn.embedded-c/
2. https://github.com/ty4tw/MQTT-SN
3. https://github.com/njh/mqtt-sn-tools
4. https://github.com/arobenko/mqtt-sn

