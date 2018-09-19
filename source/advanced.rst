
.. _advanced:

========
Advanced
========

-------------------
Shared Subscription
-------------------

*EMQX* 3.0 release supports cluster level `Shared Subscription`.Shared Subscription supports Load balancing to distribute MQTT messages between multiple subscribers in the same group::

                                ----------
                                |        | --Msg1--> Subscriber1
    Publisher--Msg1,Msg2,Msg3-->|  EMQX  | --Msg2--> Subscriber2
                                |        | --Msg3--> Subscriber3
                                ----------

Two ways to create a shared subscription:

+-----------------+-------------------------------------------+
|  Prefix         | Examples                                  |
+-----------------+-------------------------------------------+
| $queue/         | mosquitto_sub -t '$queue/topic'           |
+-----------------+-------------------------------------------+
| $share/<group>/ | mosquitto_sub -t '$share/group/topic'     |
+-----------------+-------------------------------------------+

example::

    mosquitto_sub -t '$share/group/topic'

    mosquitto_pub -t 'topic' -m msg -q 2

