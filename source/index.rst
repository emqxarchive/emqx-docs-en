.. Erlang MQTT Broker documentation master file, created by
   sphinx-quickstart on Mon Feb 22 00:46:47 2016.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

==========================
*EMQ* - Erlang MQTT Broker
==========================

*EMQ(Erlang MQTT Broker)* is a distributed, massively scalable, highly extensible MQTT message broker written in Erlang/OTP.

.. NOTE:: Adopt a shortened project name since 2.0 release: EMQ

*EMQ* is fully open source and licensed under the Apache Version 2.0. *EMQ* implements both MQTT V3.1 and V3.1.1 protocol specifications, and supports MQTT-SN, CoAP, WebSocket, STOMP and SockJS at the same time.

*EMQ* provides a scalable, reliable, enterprise-grade MQTT message Hub for IoT, M2M, Smart Hardware and Mobile Messaging Applications. Sensors, Mobiles, Web Browsers and Application Servers could be connected by *EMQ* brokers with asynchronous PUB/SUB MQTT messages.

The 1.0 release of the *EMQ* broker has scaled to 1.3 million concurrent MQTT connections on a 12 Core, 32G CentOS server.

Please visit `emqx.io`_ for more service. Follow us on Twitter: `@emqx`_


.. _emqx.io: https://www.emqx.io
.. _@emqx: https://twitter.com/emqx

.. image:: ./_static/images/emqtt.png

+---------------+-----------------------------------------+
| Homepage:     | https://www.emqx.io                         |
+---------------+-----------------------------------------+
| Downloads:    | https://www.emqx.io/downloads               |
+---------------+-----------------------------------------+
| GitHub:       | https://github.com/emqtt                |
+---------------+-----------------------------------------+
| Twitter:      | @emqtt                                  |
+---------------+-----------------------------------------+
| Forum:        | https://groups.google.com/d/forum/emqtt |
+---------------+-----------------------------------------+
| Mailing List: | emqtt@googlegroups.com                  |
+---------------+-----------------------------------------+
| Author:       | Feng Lee <feng@emqx.io>                |
+---------------+-----------------------------------------+

Contents:

.. toctree::
   :maxdepth: 2

   getstarted
   install
   config
   cluster
   bridge
   guide
   advanced
   design
   commands
   plugins
   rest
   tune
   changes
   upgrade
   mqtt
   mqtt-sn
   lwm2m

-------
License
-------

Apache License Version 2.0

