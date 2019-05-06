.. _protocol:


Protocol
^^^^^^^^


MQTT Protocol
-------------

Overview
::::::::

MQTT is a lightweight publish-subscribe mode messaging protocol which is designed for IoT applications in low-bandwidth and unstable network environments.

MQTT official website: http://mqtt.org

MQTT V3.1.1 protocol specification:  http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html

Feature
:::::::

1. Open message protocol that is easy to implement

2. publish-subscribe mode，one-to-many message publishing

3. Based on TCP/IP network connection

4. Compact message structure with 1 byte of fixed header and 2 bytes of heartbeat message, 

5. Message QoS support with reliable transmission guarantee

Application
:::::::::::

MQTT protocol is widely used in Internet of Things, Mobile Internet, Intelligent Hardware,Internet of Vehicles, Power Energy and so on.

1. M2M Communication and Big Data Acquisition in the Internet of Things

2. Android message push and WEB message push

3. Mobile instant messaging, such as Facebook Messenger

4. Intelligent hardware, smart furniture and appliances

5. Vehicle networking communication, pile collection of electric vehicles

6. Smart City, Telemedicine, Distance Education

7. Electricity, Oil and Energy industry

.. _mqtt_topic:

Topic-based Message Routing of MQTT
:::::::::::::::::::::::::::::::::::

The MQTT protocol routes messages based on Topic, which is similar to URL paths, for example::

    chat/room/1

    sensor/10/temperature

    sensor/+/temperature

    $SYS/broker/metrics/packets/received

    $SYS/broker/metrics/#

Topic distinguishes the hierarchy by '/' and supports the wildcard of'+' and '#' ::

    '+': indicates a single level of wildcard , such as a/+ matching a/x or a/y

    '#': indicates multiple levels of wildcards，such as a/# matching a/x, a/b/c/d

Subscribers and publishers communicate via topic routing messages, such as using the mosquitto command line to publish subscribed messages::

    mosquitto_sub -t a/b/+ -q 1

    mosquitto_pub -t a/b/c -m hello -q 1

.. NOTE:: Subscribers are allowed to subscribe to wildcard-containing topics, but publishers are not allowed to publish messages to wildcard-containing topics.

.. _mqtt_protocol:

MQTT V3.1.1 Protocol Packet
:::::::::::::::::::::::::::

Packet structure
''''''''''''''''

+--------------------------------------------------+
| Fixed header                                     |
+--------------------------------------------------+
| Variable header)                                 |
+--------------------------------------------------+
| Payload                                          |
+--------------------------------------------------+

Fixed header 
''''''''''''

+----------+-----+-----+-----+-----+-----+-----+-----+-----+
| Bit      |  7  |  6  |  5  |  4  |  3  |  2  |  1  |  0  |
+----------+-----+-----+-----+-----+-----+-----+-----+-----+
| byte1    |   MQTT Packet type    |         Flags         |
+----------+-----------------------+-----------------------+
| byte2... |   Remaining Length                            |
+----------+-----------------------------------------------+

Packet type
'''''''''''

+-------------+---------+----------------------+
| Name        | Value   | Description          |
+-------------+---------+----------------------+
| CONNECT     | 1       | Initiate a connection|
+-------------+---------+----------------------+
| CONNACK     | 2       | Connection receipt   |
+-------------+---------+----------------------+
| PUBLISH     | 3       | Publish message      |
+-------------+---------+----------------------+
| PUBACK      | 4       | Publish  receipt     |
+-------------+---------+----------------------+
| PUBREC      | 5       | QoS2 message receipt |
+-------------+---------+----------------------+
| PUBREL      | 6       | QoS 2message release |
+-------------+---------+----------------------+
| PUBCOMP     | 7       | QoS2 message finish  |
+-------------+---------+----------------------+
| SUBSCRIBE   | 8       | Subscribe to topics  |
+-------------+---------+----------------------+
| SUBACK      | 9       | Subscribe to receipt |
+-------------+---------+----------------------+
| UNSUBSCRIBE | 10      | Unsubscribe          |
+-------------+---------+----------------------+
| UNSUBACK    | 11      |Unsubscribe to receipt|
+-------------+---------+----------------------+
| PINGREQ     | 12      | PING request         |
+-------------+---------+----------------------+
| PINGRESP    | 13      | PING response        |
+-------------+---------+----------------------+
| DISCONNECT  | 14      | Disconnected         |
+-------------+---------+----------------------+

publish message by PUBLISH 
''''''''''''''''''''''''''

The PUBLISH packet carries a two-way published message between the client and the server. The PUBACK packet is used to confirm the QoS1 message, and the PUBREC/PUBREL/PUBCOMP packet is used for the QoS2 message flow.

PINGREQ/PINGRESP heartbeat
''''''''''''''''''''''''''

When no packet is sent, the client sends a PINGREQ heartbeat packet to the server according to the keepalive period. The server responds to the PINGRESP packet. THe sizes of PINGREQ/PINGRESP packet are both 2 bytes。

.. _mqtt_qos:

MQTT Message QoS
::::::::::::::::

MQTT message QoS guarantee is between the client and the server. The QoS level at which the subscriber receives the MQTT message ultimately depends on the QoS of the published message and the QoS of the topic subscription.

+------------------------+--------------------------+------------------------+
| QoS to publish message | QoS of topic subscription| QoS to receive message |
+------------------------+--------------------------+------------------------+
|      0                 |      0                   |      0                 |
+------------------------+--------------------------+------------------------+
|      0                 |      1                   |      0                 |
+------------------------+--------------------------+------------------------+
|      0                 |      2                   |      0                 |
+------------------------+--------------------------+------------------------+
|      1                 |      0                   |      0                 |
+------------------------+--------------------------+------------------------+
|      1                 |      1                   |      1                 |
+------------------------+--------------------------+------------------------+
|      1                 |      2                   |      1                 |
+------------------------+--------------------------+------------------------+
|      2                 |      0                   |      0                 |
+------------------------+--------------------------+------------------------+
|      2                 |      1                   |      1                 |
+------------------------+--------------------------+------------------------+
|      2                 |      2                   |      2                 |
+------------------------+--------------------------+------------------------+

Qos0 message publish and subscribe
''''''''''''''''''''''''''''''''''

.. image:: ./_static/images/qos0_seq.png

Qos1 message publish and subscribe
''''''''''''''''''''''''''''''''''

.. image:: ./_static/images/qos1_seq.png

Qos2 message publish and subscribe
''''''''''''''''''''''''''''''''''

.. image:: ./_static/images/qos2_seq.png

.. _mqtt_clean_session:

MQTT Clean Session
::::::::::::::::::

When the MQTT client initiates a CONNECT request to the server, the session can be set via the 'Clean Session' flag.

'Clean Session' is set to 0, which means that a persistent session is created. When the client is disconnected, the session still keeps and saves the offline message until the session times out.

'Clean Session' is set to 1, which means that a new temporary session is created, and the session is automatically destroyed when the client is disconnected.

.. _mqtt_keepalive:

MQTT connection keep-alive heartbeat
::::::::::::::::::::::::::::::::::::

When the MQTT client initiates a CONNECT request to the server, the keepalive period is set by the KeepAlive parameter.

When no packet is sent, the client sends a 2-byte PINGREQ heartbeat packet periodically according to the KeepAlive period. After receiving the PINGREQ packet, the server replies with a 2-byte PINGRESP packet.

During the 1.5 heartbeat periods, if the server does not receive the client's subscription message or the PINGREQ heartbeat message, the TCP connection of client is  disconnected by the active heartbeat timeout.

.. NOTE:: The timeout of emqttd broker is set to a maximum of 2.5 heartbeat cycle by default.

.. _mqtt_willmsg:

MQTT Last Will
::::::::::::::

When the MQTT client makes a CONNECT request to the server, it can set whether to send the flag of Will Message , as well as the Topic and Payload.

When the MQTT client is abnormally offline (the DISCONNECT message is not sent to the server before the client disconnects), the MQTT message server will publish a will message.

.. _mqtt_retained_msg:

MQTT Retained Message
:::::::::::::::::::::

When the MQTT client publishes a  message to the server, Retained Message flag can be set. The Retained Message resides on the message server, and subsequent subscribers can still receive the message when they subscribe to the topic.

For example, the mosquitto command line publishes a reserved message to the topic'a/b/c'::

    mosquitto_pub -r -q 1 -t a/b/c -m 'hello'

If the subsequent MQTT client subscribes to the topic 'a/b/c', the message can still be received::

    $ mosquitto_sub -t a/b/c -q 1
    hello

There are two ways to clear a Retained Message:

1. The client publishes an empty message to the topic with the reserved message:::

    mosquitto_pub -r -q 1 -t a/b/c -m ''

2. The broker sets the expiration time for the reserved message.

.. _mqtt_websocket:

MQTT WebSocket connection
:::::::::::::::::::::::::

In addition to supporting the TCP transport layer, the MQTT protocol also supports websockets as the transport layer. The WebSocket browser enables direct connection to the MQTT messaging server and  communicate with other MQTT clients through publish-subscribe mode.

The the binary mode must be used in WebSocket connection of the MQTT protocol with the sub-protocol Header::

    Sec-WebSocket-Protocol: mqttv3.1 或 mqttv3.1.1

.. _mqtt_client_libraries:


MQTT protocol client library
::::::::::::::::::::::::::::

emqtt client library
''''''''''''''''''''

emqtt project team: https://github.com/emqtt

+--------------------+------------------------------------+
| `emqttc`_          | Erlang MQTT client library         |
+--------------------+------------------------------------+
| `CocoaMQTT`_       | Swift language MQTT client library |
+--------------------+------------------------------------+
| `QMQTT`_           | QTQT framework MQTT client library |
+--------------------+------------------------------------+

Eclipse Paho client library
'''''''''''''''''''''''''''

Paho official website: http://www.eclipse.org/paho/

mqtt.org official website client library
''''''''''''''''''''''''''''''''''''''''

mqtt.org: https://github.com/mqtt/mqtt.github.io/wiki/libraries

.. _mqtt_vs_xmpp:

Comparison between MQTT and XMPP protocols
::::::::::::::::::::::::::::::::::::::::::

The MQTT protocol has the features of simple, lightweight, and flexible in routing. It will completely replace the XMPP protocol in the PC era in the field of mobile Internet and IoT messaging:

1. The MQTT protocol has a fixed byte header and a two-byte heartbeat packet. The size is small and easy to decode. The XMPP protocol is based on heavy XML, with large packet size is large and cumbersome interaction.

2. The MQTT protocol is based on topic publish-subscribe message routing which is more flexible than XMPP's jid-based point-to-point message routing.

3. The MQTT protocol does not define a packet format and can carry different types of packets such as JSON and binary. The XMPP protocol uses XML to carry messages, and the binary must be processed by Base64 encoding.

4. The MQTT protocol supports messaging receipt and QoS guarantees, and the XMPP master protocol does not define a similar mechanism. The MQTT protocol has better message reliability guarantees.
