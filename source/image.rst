
.. _image:

================
QingCloud image
================

* EMQ* Message Server Version 1.1.2 officially landed in the `QingCloud` image market. Users can directly create images to enable EMQ message server.

.. image:: ./_static/images/imagemarket.png

Image Attributes
--------

+--------------+---------------------------------------------------+
| Attributes   | Value                                             |
+--------------+---------------------------------------------------+
| Name         | EMQ Ubuntu Image-Million-level distributed IoT MQTT|
               |  message server                                   |
+--------------+---------------------------------------------------+
| Cost         | Free                                              |
+--------------+---------------------------------------------------+
| Category     | IoT                                               |
+--------------+---------------------------------------------------+
|              | A million-level connection and distributed cluster|
|              | an open source MQTT message server by the pattern |
|   Abstract   | of release and subscription.  Completely support  |
|              | for Internet of Things protocols such as MQTT,    |
|              | MQTT-SN, and CoAP.                                |
+--------------+---------------------------------------------------+
| Developer    | feng@emqx.io                                      |
+--------------+---------------------------------------------------+
| Submission time | 2016-07-11                                     |
+--------------+---------------------------------------------------+
| Update time    | 2016-07-11                                      |
+--------------+---------------------------------------------------+

Image description
--------

1. The image is created based on EMQ version 1.1.2. After startup, the MQTT server is monitored on TCP 1883 port.

2. EMQ Server WEB Console, port 18083. Default administrator: admin, password: public, please change the default password after the image is launched.

3. The image does not have MQTT authentication by default which allows the MQTT client to log in anonymously;

4. The EMQ program is deployed in the /opt/emqttd/ directory. Users can enable various authentication methods such as HTTP, PostgreSQL, MySQL, Redis, and MongoDB by configuring etc/emqttd.config or loading plug-ins.

5. For detailed configuration and application of the EMQ message server, please refer to the documentation: http://emqtt.com/docs/

This image is jointly supported by emqtt.com and QingCloud. User QQ group: 196066320

Current version of image
------------

Image components:

1. Erlang/OTP 18.3 version environment.

2. EMQ 1.1.2 version

Image configuration instructions:

1. The  ports 1883 (MQTT), 8083 (WebSocket), and 18083 (Dashboard Console) is opened in firewall.

2. Dashboard console, port 18083. Default administrator: admin, password: public, please change the password immediately after the image is launched!

3. The image allows 10,000 concurrent MQTT connections by default, Maximum configurable to 100,000 connections.

4. Image memory oonsumption: 50,000 connections / 1G memory. User can adjust the memory based on the MQTT message throughput.

EMQ Manual start and stop
------------

.. code::

    systemctl start emqttd
    systemctl stop emqttd

.. _QingCloud: https://www.qingcloud.com

