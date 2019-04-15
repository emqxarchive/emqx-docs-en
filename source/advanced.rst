
.. _advanced:

==================
Advanced Features
==================

-------------------
Shared Subscription
-------------------

*EMQ X* 3.0 supports shared subscription at cluster level. Shared Subscription supports to distribute messages between multiple subscribers by packet load balancing. ::

                                -----------
                                |         | --Msg1--> Subscriber1
    Publisher--Msg1,Msg2,Msg3-->|  EMQ X  | --Msg2--> Subscriber2
                                |         | --Msg3--> Subscriber3
                                -----------

A shared subscription for any :code:`topic` can be created in two ways:

1. By subscribing to :code:`$queue/topic`
2. By subscribing to :code:`$share/group/topic`

Implementation detail: :code:`$queue/topic` is internally considered equivalent to :code:`$share/$queue/topic`

The publisher need not know that the topic is being load balanced between multiple subscribers.

The :code:`group` can be chosen arbitrarily. A group is created on-demand when
subscription is requested. Load balancing happens within a group.

If multiple groups are created on the broker, each group will receive one copy of the messages published.

The topic may contain wildcards (:code:`+` or :code:`#`) in it as per usual rules.
Group is not allowed to contain the characters :code:`+`,:code:`#`, or :code:`/` in it.

Example:

.. code-block:: shell

    # This example is designed to be copy-pasteable.
    # Set the env vars EMQX_ADDRESS and EMQX_PORT before running this if necessary.

    # Set default values if not defined. (Bash syntax. Leading : is not a typo.)
    : "${EMQX_ADDRESS:=127.0.0.1}" "${EMQX_PORT:=1883}"

    mosquitto_sub -t '$share/workers/some/long/topic' -i worker1 -h $EMQX_ADDRESS -p $EMQX_PORT
    mosquitto_sub -t '$share/workers/some/long/topic' -i worker2 -h $EMQX_ADDRESS -p $EMQX_PORT
    mosquitto_sub -t '$share/workers/some/long/topic' -i worker3 -h $EMQX_ADDRESS -p $EMQX_PORT

    for i in `seq 1 10`; do mosquitto_pub -t 'some/long/topic' -m "msg $i" -q 2; done;

This defines a group called :code:`workers` and load balances messages
published on :code:`some/long/topic` between the 3 clients subscribing to this
group.
