
.. _protocol:


Protocol
^^^^^^^^

MQTT Protocol
--------------


MQTT-SN Protocol
-----------------

MQTT-SN stands for "MQTT for Sensor Networks" which aims at embedded devices on non-TCP/IP networks, such as ZIGBEE. Its offical site says:

MQTT-SN is a publish/subscribe messaging protocol for wireless sensor networks (WSN), with the aim of extending the MQTT protocol beyond the reach of TCP/IP infrastructure for Sensor and Actuator solutions.

MQTT-SN specification can be downloaded from http://mqtt.org/new/wp-content/uploads/2009/06/MQTT-SN_spec_v1.2.pdf.

MQTT-SN vs MQTT
::::::::::::::::

MQTT-SN looks similar to MQTT in most parts, such as Will message, such as Connect/Subscribe/Publish command.

The very difference between MQTT-SN and MQTT is the TopicId which replaces topic name in MQTT. TopicId is a 16 bits integer which stands for a topic name. Device and broker use REGISTER command to negotiate the mapping bewteen TopicId and topic name.

MQTT-SN is able to update Will message, even delete it. But MQTT is not allowed to change Will which is set in CONNECT command only.

MQTT-SN introduces gateways in its network. Gateway translates between MQTT-SN and MQTT, exchanges messages between device and MQTT broker. And there is a mechanism that called gateway discovery, which enables device to find gateways automatically.

MQTT-SN supports sleeping client feature which allows device to shutdown itself to save power for a while. Gateway needs to buffer downlink publish messages for sleeping devices, and pushes these messages to devices once they are awake.

EMQX-SN Plugin
:::::::::::::::

EMQX-SN is an *EMQ X* plugin which implements most features of MQTT-SN. It serves as an MQTT-SN gateway on cloud, who is the neighbor of *EMQ X* Broker.

Configurations
'''''''''''''''

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
| mqtt.sn.username            | This parameter is optional. If specified, emqx-sn will connect *EMQ X*   |
|                             | Broker with this username. It is useful if any auth plug-in is enabled.  |
+-----------------------------+--------------------------------------------------------------------------+
| mqtt.sn.password            | This parameter is optional. Pair with username above.                    |
+-----------------------------+--------------------------------------------------------------------------+

Load Plugin
''''''''''''

.. code-block:: console

    ./bin/emqx_ctl plugins load emqx_sn

MQTT-SN Client Library
:::::::::::::::::::::::

1. https://github.com/eclipse/paho.mqtt-sn.embedded-c/
2. https://github.com/ty4tw/MQTT-SN
3. https://github.com/njh/mqtt-sn-tools
4. https://github.com/arobenko/mqtt-sn

LWM2M Protocol
---------------

Lightweight M2M (LWM2M) is a set of protocols defined by the Open Mobile Alliance (OMA) for machine-to-machine (M2M) or Internet of Things (IoT) device management and communications. It can be found `here <http://www.openmobilealliance.org/wp/>`_ .

LWM2M is based on CoAP protocol, and can be carried on UDP or SMS.


There are two types of servers:

- LWM2M BOOTSTRAP SERVER. emqx-lwm2m does not support such kind of server.
- LWM2M SERVER. emqx-lwm2m implements most features of this server type, except for SMS binding.

LWM2M defines service on a device as Object and Resource, which is represented in an XML file. A list of registered XML Objects can be found `here <http://www.openmobilealliance.org/wp/OMNA/LwM2M/LwM2MRegistry.html>`_ .

EMQX-LWM2M plugin
::::::::::::::::::

EMQX-LWM2M is an *EMQ X* plugin，which implements most LWM2M features. MQTT client is able to access LWM2M device through EMQX-LWM2M plugin, by sending a command and reading its response.

Convertion between MQTT and LWM2M
::::::::::::::::::::::::::::::::::

Commands from MQTT client to LWM2M device is carried in following topic:

.. code-block::

    "lwm2m/{?device_end_point_name}/command".

And MQTT Payload will be a json format data which specifies the command. Please refer to emqx-lwm2m document for details.
    


Response from LWM2M device to MQTT client is carried in following topic:
    
.. code-block::

    "lwm2m/{?device_end_point_name}/response".

And MQTT payload will be a json format data which specifies the command. Please refer to emqx-lwm2m document for details.
    

Configurations
'''''''''''''''

File: etc/emqx_lwm2m.conf::

    lwm2m.port = 5683
       
    lwm2m.certfile = etc/certs/cert.pem

    lwm2m.keyfile = etc/certs/key.pem

    lwm2m.xml_dir =  etc/lwm2m_xml

+-----------------------+----------------------------------------------------------------------------+
| lwm2m.port            | LWM2M udp port. Port 5783 is used to prevent conflict against emqx-coap    |
+-----------------------+----------------------------------------------------------------------------+
| lwm2m.certfile        | DTLS certificate                                                           |
+-----------------------+----------------------------------------------------------------------------+
| lwm2m.keyfile         | DTLS private key                                                           |
+-----------------------+----------------------------------------------------------------------------+
| lwm2m.xml_dir         | A directory to store XML files which define LWM2M Objects                  |
+-----------------------+----------------------------------------------------------------------------+

Start emqx-lwm2m
''''''''''''''''

.. code-block::

    ./bin/emqx_ctl plugins load emqx_lwm2m

LWM2M clients
'''''''''''''

- https://github.com/eclipse/wakaama
- https://github.com/OpenMobileAlliance/OMA-LWM2M-DevKit 
- https://github.com/AVSystem/Anjay
- https://github.com/ConnectivityFoundry/AwaLWM2M
- http://www.eclipse.org/leshan/
