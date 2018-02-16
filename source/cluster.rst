
.. _clustering:

==========
Clustering
==========

----------------------
Distributed Erlang/OTP
----------------------

Erlang/OTP is a concurrent, fault-tolerant, distributed programming platform. A distributed Erlang/OTP system consists of a number of Erlang runtime systems called 'node'. Nodes connect to each other with TCP/IP sockets and communicate by Message Passing.

.. code::

    ---------         ---------
    | Node1 | --------| Node2 |
    ---------         ---------
        |     \     /    |
        |       \ /      |
        |       / \      |
        |     /     \    |
    ---------         ---------
    | Node3 | --------| Node4 |
    ---------         ---------

Node
----

An erlang runtime system called 'node' is identified by a unique name like email addreass. Erlang nodes communicate with each other by the name.

Suppose we start four Erlang nodes on localhost:

.. code-block:: bash

    erl -name node1@127.0.0.1
    erl -name node2@127.0.0.1
    erl -name node3@127.0.0.1
    erl -name node4@127.0.0.1

connect all the nodes::

    (node1@127.0.0.1)1> net_kernel:connect_node('node2@127.0.0.1').
    true
    (node1@127.0.0.1)2> net_kernel:connect_node('node3@127.0.0.1').
    true
    (node1@127.0.0.1)3> net_kernel:connect_node('node4@127.0.0.1').
    true
    (node1@127.0.0.1)4> nodes().
    ['node2@127.0.0.1','node3@127.0.0.1','node4@127.0.0.1']

epmd
----

epmd(Erlang Port Mapper Daemon) is a daemon service that is responsible for mapping node names to machine addresses(TCP sockets). The daemon is started automatically on every host where an Erlang node started.

.. code-block:: bash

    (node1@127.0.0.1)6> net_adm:names().
    {ok,[{"node1",62740},
         {"node2",62746},
         {"node3",62877},
         {"node4",62895}]}

Cookie
------

Erlang nodes authenticate each other by a magic cookie when communicating. The cookie could be configured by::

    1. $HOME/.erlang.cookie

    2. erl -setcookie <Cookie>

.. NOTE:: Content of this chapter is from: http://erlang.org/doc/reference_manual/distributed.html

--------------
Cluster Design
--------------

The cluster architecture of emqttd broker is based on distributed Erlang/OTP and Mnesia database.

The cluster design could be summarized by the following two rules:

1. When a MQTT client SUBSCRIBE a Topic on a node, the node will tell all the other nodes in the cluster: I subscribed a Topic.

2. When a MQTT Client PUBLISH a message to a node, the node will lookup the Topic table and forward the message to nodes that subscribed the Topic.

Finally there will be a global route table(Topic -> Node) that replicated to all nodes in the cluster::

    topic1 -> node1, node2
    topic2 -> node3
    topic3 -> node2, node4

Topic Trie and Route Table
--------------------------

Every node in the cluster will store a topic trie and route table in mnesia database.

Suppose that we create subscriptions:

+----------------+-------------+----------------------------+
| Client         | Node        |  Topics                    |
+================+=============+============================+
| client1        | node1       | t/+/x, t/+/y               |
+----------------+-------------+----------------------------+
| client2        | node2       | t/#                        |
+----------------+-------------+----------------------------+
| client3        | node3       | t/+/x, t/a                 |
+----------------+-------------+----------------------------+

Finally the topic trie and route table in the cluster::

    --------------------------
    |          t             |
    |         / \            |
    |        +   #           |
    |      /  \              |
    |    x      y            |
    --------------------------
    | t/+/x -> node1, node3  |
    | t/+/y -> node1         |
    | t/#   -> node2         |
    | t/a   -> node3         |
    --------------------------

Message Route and Deliver
--------------------------

The brokers in the cluster route messages by topic trie and route table, deliver messages to MQTT clients by subscriptions. Subscriptions are mapping from topic to subscribers, are stored only in the local node, will not be replicated to other nodes.

Suppose client1 PUBLISH a message to the topic 't/a', the message Route and Deliver process::

    title: Message Route and Deliver

    client1->node1: Publish[t/a]
    node1-->node2: Route[t/#]
    node1-->node3: Route[t/a]
    node2-->client2: Deliver[t/#]
    node3-->client3: Deliver[t/a]

.. image:: _static/images/route.png

-------------
Cluster Setup
-------------

Suppose we deploy two nodes cluster on s1.emqtt.io, s2.emqtt.io:

+-----------------------+-----------------+---------------------+
| Node                  | Host(FQDN)      |  IP and Port        |
+-----------------------+-----------------+---------------------+
| emq@s1.emqtt.io or    | s1.emqtt.io     | 192.168.0.10:1883   |
| emq@192.168.0.10      |                 |                     |
+-----------------------+-----------------+---------------------+
| emq@s2.emqtt.io or    | s2.emqtt.io     | 192.168.0.20:1883   |
| emq@192.168.0.20      |                 |                     |
+-----------------------+-----------------+---------------------+

.. WARNING:: The node name is Name@Host, where Host is IP address or the fully qualified host name.

emq@s1.emqtt.io config
----------------------

etc/emq.conf::

    node.name = emq@s1.emqtt.io

    or

    node.name = emq@192.168.0.10

.. WARNING:: The name cannot be changed after node joined the cluster.

emq@s2.emqtt.io config
-----------------------

etc/emq.conf::

    node.name = emq@s2.emqtt.io

    or

    node.name = emq@192.168.0.20

Join the cluster
----------------

Start the two broker nodes, and 'cluster join ' on emqttd@s2.emqtt.io::

    $ ./bin/emqttd_ctl cluster join emq@s1.emqtt.io

    Join the cluster successfully.
    Cluster status: [{running_nodes,['emq@s1.emqtt.io','emq@s2.emqtt.io']}]

Or 'cluster join' on emq@s1.emqtt.io::

    $ ./bin/emqttd_ctl cluster join emq@s2.emqtt.io

    Join the cluster successfully.
    Cluster status: [{running_nodes,['emq@s1.emqtt.io','emq@s2.emqtt.io']}]

Query the cluster status::

    $ ./bin/emqttd_ctl cluster status

    Cluster status: [{running_nodes,['emq@s1.emqtt.io','emq@s2.emqtt.io']}]

Leave the cluster
-----------------

Two ways to leave the cluster:

1. leave: this node leaves the cluster

2. remove: remove other nodes from the cluster

emq@s2.emqtt.io node tries to leave the cluster::

    $ ./bin/emqttd_ctl cluster leave

Or remove emq@s2.emqtt.io node from the cluster on emq@s1.emqtt.io::

    $ ./bin/emqttd_ctl cluster remove emq@s2.emqtt.io

------------------------------
Node Discovery and Autocluster
------------------------------

EMQ R2.3 supports node discovery and autocluster with various strategies:

+------------+---------------------------------+
| Strategy   | Description                     |
+============+=================================+
| static     | Autocluster by static node list |
+------------+---------------------------------+
| mcast      | Autocluster by UDP Multicast    |
+------------+---------------------------------+
| dns        | Autocluster by DNS A Record     |
+------------+---------------------------------+
| etcd       | Autocluster using etcd          |
+------------+---------------------------------+
| k8s        | Autocluster on Kubernetes       |
+------------+---------------------------------+

Autocluster by static node list
-------------------------------

.. code-block:: properties

    cluster.discovery = static

    ##--------------------------------------------------------------------
    ## Cluster with static node list

    cluster.static.seeds = emq1@127.0.0.1,ekka2@127.0.0.1

Autocluster by IP Multicast
---------------------------

.. code-block:: properties

    cluster.discovery = mcast

    ##--------------------------------------------------------------------
    ## Cluster with multicast

    cluster.mcast.addr = 239.192.0.1

    cluster.mcast.ports = 4369,4370

    cluster.mcast.iface = 0.0.0.0

    cluster.mcast.ttl = 255

    cluster.mcast.loop = on

Autocluster by DNS A Record
---------------------------

.. code-block:: properties

    cluster.discovery = dns

    ##--------------------------------------------------------------------
    ## Cluster with DNS

    cluster.dns.name = localhost

    cluster.dns.app  = ekka

Autocluster using etcd
----------------------

.. code-block:: properties

    cluster.discovery = etcd

    ##--------------------------------------------------------------------
    ## Cluster with Etcd

    cluster.etcd.server = http://127.0.0.1:2379

    cluster.etcd.prefix = emqcl

    cluster.etcd.node_ttl = 1m

Autocluster on Kubernetes
-------------------------

.. code-block:: properties

    cluster.discovery = k8s

    ##--------------------------------------------------------------------
    ## Cluster with k8s

    cluster.k8s.apiserver = http://10.110.111.204:8080

    cluster.k8s.service_name = ekka

    ## Address Type: ip | dns
    cluster.k8s.address_type = ip

    ## The Erlang application name
    cluster.k8s.app_name = ekka

.. _cluster_netsplit:

------------------------------
Network Partition and Autoheal
------------------------------

Enable autoheal of Network Partition:

.. code-block:: properties

    cluster.autoheal = on

When network partition occurs, the following steps are performed to heal the cluster if autoheal is enabled:

1. Node reports the partitions to a leader node which has the oldest guid.

2. Leader node create a global netsplit view and choose one node in the majority as coordinator.

3. Leader node requests the coordinator to autoheal the network partition.

4. Coordinator node reboots all the nodes in the minority side.

-----------------------
Node down and Autoclean
-----------------------

A down node will be removed from the cluster if autoclean is enabled:

.. code-block:: properties

    cluster.autoclean = 5m

.. _cluster_session:

--------------------
Session across Nodes
--------------------

The persistent MQTT sessions (clean session = false) are across nodes in the cluster.

If a persistent MQTT client connected to node1 first, then disconnected and connects to node2, the MQTT connection and session will be located on different nodes::

                                      node1
                                   -----------
                               |-->| session |
                               |   -----------
                 node2         |
              --------------   |
     client-->| connection |<--|
              --------------

------------
The Firewall
------------

If there is a firewall between clustered nodes, the cluster requires to open 4369 port used by epmd daemon, and a port segment for nodes' communication.

Configure the port segment in releases/2.0/sys.config, for example:

.. code-block:: erlang

    [{kernel, [
        ...
        {inet_dist_listen_min, 20000},
        {inet_dist_listen_max, 21000}
     ]},
     ...

------------------
Network Partitions
------------------

The emqttd 1.0 cluster requires reliable network to avoid network partitions. The cluster will not recover from a network partition automatically.

If a network partition occures, there will be critical logs in log/emqttd_error.log::

    Mnesia inconsistent_database event: running_partitioned_network, emqttd@host

To recover from a network partition, you have to stop the nodes in a partition, clean the 'data/mneisa' of these nodes and reboot to join the cluster again.

-----------------------
Consistent Hash and DHT
-----------------------

Consistent Hash and DHT are popular in the design of NoSQL databases. Cluster of emqttd broker could support 10 million size of global routing table now. We could use the Consistent Hash or DHT to partition the routing table, and evolve the cluster to larger size.

