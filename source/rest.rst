
.. _rest_api:

========
REST API
========

The REST API allows you to query MQTT clients, sessions, subscriptions, and routes. You can also query and monitor the metrics and statistics of the broker.

--------
Base URL
--------

All REST APIs in the documentation have the following base URL::

    http(s)://host:8080/api/v3/

--------------------
Basic Authentication
--------------------

The HTTP requests to the REST API are protected with HTTP Basic authentication. You can create an application in Dashboard, using appid and appsecret to authenticate.  For example:

.. code-block:: bash

    curl -v --basic -u <appid>:<appsecret> -k http://localhost:8080/api/v3/brokers

----------
API's Info
----------

List all API describe
----------------------



Definition::

    GET api/v3/


Example Request::

    GET api/v3/


Response:

.. code-block:: json

    [
      {
            "name": "list_brokers",
            "method": "GET",
            "path": "/brokers/",
            "descr": "A list of brokers in the cluster"
      },
      {
            "name": "list_connections",
            "method": "GET",
            "path": "/connections/",
            "descr": "A list of connections in the cluster"
      },
      {
            "name": "list_node_connections",
            "method": "GET",
            "path": "nodes/:node/connections/",
            "descr": "A list of connections on a node"
      },
      {
            "name": "list_listeners",
            "method": "GET",
            "path": "/listeners/",
            "descr": "A list of listeners in the cluster"
      },
      {
            "name": "list_node_listeners",
            "method": "GET",
            "path": "/nodes/:node/listeners",
            "descr": "A list of listeners on the node"
      },
      {
            "name": "list_node_metrics",
            "method": "GET",
            "path": "/nodes/:node/metrics/",
            "descr": "A list of metrics of a node"
      },
      {
            "name": "list_all_metrics",
            "method": "GET",
            "path": "/metrics/",
            "descr": "A list of metrics of all nodes in the cluster"
      },
      {
            "name": "list_nodes",
            "method": "GET",
            "path": "/nodes/",
            "descr": "A list of nodes in the cluster"
      },
      {
            "name": "list_sessions",
            "method": "GET",
            "path": "/sessions/",
            "descr": "A list of sessions in the cluster"
      },
      {
            "name": "list_node_sessions",
            "method": "GET",
            "path": "nodes/:node/sessions/",
            "descr": "A list of sessions on a node"
      },
      {
            "name": "lookup_node_stats",
            "method": "GET",
            "path": "/nodes/:node/stats/",
            "descr": "A list of stats of a node"
      },
      {
            "name": "list_stats",
            "method": "GET",
            "path": "/stats/",
            "descr": "A list of stats of all nodes in the cluster"
      },
      {
            "name": "list_subscriptions",
            "method": "GET",
            "path": "/subscriptions/",
            "descr": "A list of subscriptions in the cluster"
      },
      {
            "name": "lookup_client_subscriptions",
            "method": "GET",
            "path": "/subscriptions/:clientid",
            "descr": "A list of subscriptions of a client"
      },
      {
            "name": "lookup_client_subscriptions_with_node",
            "method": "GET",
            "path": "/nodes/:node/subscriptions/:clientid",
            "descr": "A list of subscriptions of a client on the node"
      },
      {
            "name": "list_node_subscriptions",
            "method": "GET",
            "path": "/nodes/:node/subscriptions/",
            "descr": "A list of subscriptions on a node"
      },
      {
            "name": "add_app",
            "method": "POST",
            "path": "/apps/",
            "descr": "Add Application"
      },
      {
            "name": "auth_user",
            "method": "POST",
            "path": "/auth",
            "descr": "Authenticate an user"
      },
      {
            "name": "change_pwd",
            "method": "PUT",
            "path": "/change_pwd/:username",
            "descr": "Change password for an user"
      },
      {
            "name": "clean_acl_cache",
            "method": "DELETE",
            "path": "/connections/:clientid/acl/:topic",
            "descr": "Clean ACL cache of a connection"
      },
      {
            "name": "create_user",
            "method": "POST",
            "path": "/users/",
            "descr": "Create an user"
      },
      {
            "name": "create_banned",
            "method": "POST",
            "path": "/banned/",
            "descr": "Create banned"
      },
      {
            "name": "del_app",
            "method": "DELETE",
            "path": "/apps/:appid",
            "descr": "Delete Application"
      },
      {
            "name": "delete_user",
            "method": "DELETE",
            "path": "/users/:name",
            "descr": "Delete an user"
      },
      {
            "name": "delete_banned",
            "method": "DELETE",
            "path": "/banned/:who",
            "descr": "Delete banned"
      },
      {
            "name": "get_all_configs",
            "method": "GET",
            "path": "/configs/",
            "descr": "Get all configs"
      },
      {
            "name": "get_all_configs",
            "method": "GET",
            "path": "/nodes/:node/configs/",
            "descr": "Get all configs of a node"
      },
      {
            "name": "get_broker",
            "method": "GET",
            "path": "/brokers/:node",
            "descr": "Get broker info of a node"
      },
      {
            "name": "get_plugin_configs",
            "method": "GET",
            "path": "/nodes/:node/plugin_configs/:plugin",
            "descr": "Get configurations of a plugin on the node"
      },
      {
            "name": "kickout_connection",
            "method": "DELETE",
            "path": "/connections/:clientid",
            "descr": "Kick out a connection"
      },
      {
            "name": "list_apps",
            "method": "GET",
            "path": "/apps/",
            "descr": "List Applications"
      },
      {
            "name": "list_node_alarms",
            "method": "GET",
            "path": "/alarms/:node",
            "descr": "List alarms of a node"
      },
      {
            "name": "list_all_alarms",
            "method": "GET",
            "path": "/alarms/",
            "descr": "List all alarms"
      },
      {
            "name": "list_all_plugins",
            "method": "GET",
            "path": "/plugins/",
            "descr": "List all plugins in the cluster"
      },
      {
            "name": "list_node_plugins",
            "method": "GET",
            "path": "/nodes/:node/plugins/",
            "descr": "List all plugins on a node"
      },
      {
            "name": "list_banned",
            "method": "GET",
            "path": "/banned/",
            "descr": "List banned"
      },
      {
            "name": "list_routes",
            "method": "GET",
            "path": "/routes/",
            "descr": "List routes"
      },
      {
            "name": "list_users",
            "method": "GET",
            "path": "/users/",
            "descr": "List users"
      },
      {
            "name": "load_plugin",
            "method": "PUT",
            "path": "/nodes/:node/plugins/:plugin/load",
            "descr": "Load a plugin"
      },
      {
            "name": "lookup_app",
            "method": "GET",
            "path": "/apps/:appid",
            "descr": "Lookup Application"
      },
      {
            "name": "lookup_connections",
            "method": "GET",
            "path": "/connections/:clientid",
            "descr": "Lookup a connection in the cluster"
      },
      {
            "name": "lookup_node_connections",
            "method": "GET",
            "path": "nodes/:node/connections/:clientid",
            "descr": "Lookup a connection on node"
      },
      {
            "name": "get_node",
            "method": "GET",
            "path": "/nodes/:node",
            "descr": "Lookup a node in the cluster"
      },
      {
            "name": "lookup_session",
            "method": "GET",
            "path": "/sessions/:clientid",
            "descr": "Lookup a session in the cluster"
      },
      {
            "name": "lookup_node_session",
            "method": "GET",
            "path": "nodes/:node/sessions/:clientid",
            "descr": "Lookup a session on the node"
      },
      {
            "name": "lookup_routes",
            "method": "GET",
            "path": "/routes/:topic",
            "descr": "Lookup routes to a topic"
      },
      {
            "name": "mqtt_publish",
            "method": "POST",
            "path": "/mqtt/publish",
            "descr": "Publish a MQTT message"
      },
      {
            "name": "mqtt_subscribe",
            "method": "POST",
            "path": "/mqtt/subscribe",
            "descr": "Subscribe a topic"
      },
      {
            "name": "unload_plugin",
            "method": "PUT",
            "path": "/nodes/:node/plugins/:plugin/unload",
            "descr": "Unload a plugin"
      },
      {
            "name": "mqtt_unsubscribe",
            "method": "POST",
            "path": "/mqtt/unsubscribe",
            "descr": "Unsubscribe a topic"
      },
      {
            "name": "update_app",
            "method": "PUT",
            "path": "/apps/:appid",
            "descr": "Update Application"
      },
      {
            "name": "update_user",
            "method": "PUT",
            "path": "/users/:name",
            "descr": "Update an user"
      },
      {
            "name": "update_config",
            "method": "PUT",
            "path": "/configs/:app",
            "descr": "Update config of an application in the cluster"
      },
      {
            "name": "update_node_config",
            "method": "PUT",
            "path": "/nodes/:node/configs/:app",
            "descr": "Update config of an application on a node"
      },
      {
            "name": "update_plugin_configs",
            "method": "PUT",
            "path": "/nodes/:node/plugin_configs/:plugin",
            "descr": "Update configurations of a plugin on the node"
      }
    ]





-----------------
Cluster and Node
-----------------

List all Cluster
-----------------



Definition::

    GET api/v3/brokers/


Example Request::

    GET api/v3/brokers/


Response:

.. code-block:: json

    [
      {
            "datetime": "2018-09-14 10:23:04",
            "node": "emqx@127.0.0.1",
            "node_status": "Running",
            "otp_release": "R21/10.0.5",
            "sysdescr": "EMQ X Broker",
            "uptime": "3 days,18 hours, 25 minutes, 11 seconds",
            "version": "3.0"
      }
    ]





Retrieve a Node's Info
----------------------



Definition::

    GET api/v3/brokers/${node}


Example Request::

    GET api/v3/brokers/emqx@127.0.0.1


Response:

.. code-block:: json

    {
      "datetime": "2018-09-14 10:23:04",
      "node_status": "Running",
      "otp_release": "R21/10.0.5",
      "sysdescr": "EMQ X Broker",
      "uptime": "3 days,18 hours, 25 minutes, 11 seconds",
      "version": "3.0"
    }




List all Nodes'statistics in the Cluster
-----------------------------------------



Definition::

    GET api/v3/nodes/


Example Request::

    GET api/v3/nodes/


Response:

.. code-block:: json

    [
      {
            "connections": 2,
            "load1": "2.50",
            "load15": "2.09",
            "load5": "2.23",
            "max_fds": 7168,
            "memory_total": "77.45M",
            "memory_used": "59.81M",
            "name": "emqx@127.0.0.1",
            "node": "emqx@127.0.0.1",
            "node_status": "Running",
            "otp_release": "R21/10.0.5",
            "process_available": 262144,
            "process_used": 331,
            "uptime": "3 days,18 hours, 25 minutes, 11 seconds",
            "version": "3.0"
      }
    ]




Retrieve a node's statistics
-----------------------------



Definition::

    GET api/v3/nodes/${node}


Example Request::

    GET api/v3/nodes/emqx@127.0.0.1


Response:

.. code-block:: json

    {
      "connections": 2,
      "load1": "2.50",
      "load15": "2.09",
      "load5": "2.23",
      "max_fds": 7168,
      "memory_total": 81211392,
      "memory_used": 62588480,
      "name": "emqx@127.0.0.1",
      "node_status": "Running",
      "otp_release": "R21/10.0.5",
      "process_available": 262144,
      "process_used": 331,
      "uptime": "3 days,18 hours, 25 minutes, 11 seconds",
      "version": "3.0"
    }




------------
Connections
------------

List all Connections in the Cluster
------------------------------------



Definition::

    GET api/v3/connections/


Example Request::

    GET api/v3/connections/?_page=1&_limit=10000


Response:

.. code-block:: json

    {
      "items": [
            {
                  "clean_start": true,
                  "client_id": "emqx-api-test:v1",
                  "connected_at": "2018-09-14 10:23:04",
                  "ipaddress": "127.0.0.1",
                  "is_bridge": false,
                  "is_super": false,
                  "keepalive": 60,
                  "mountpoint": "undefined",
                  "node": "emqx@127.0.0.1",
                  "peercert": "nossl",
                  "port": 60492,
                  "proto_name": "MQTT",
                  "proto_ver": 4,
                  "username": "emqx-api-test:v1",
                  "will_topic": "undefined",
                  "zone": "external"
            },
            {
                  "clean_start": true,
                  "client_id": "mqttjs_68980a5d",
                  "connected_at": "2018-09-14 10:23:04",
                  "ipaddress": "127.0.0.1",
                  "is_bridge": false,
                  "is_super": false,
                  "keepalive": 60,
                  "mountpoint": "undefined",
                  "node": "emqx@127.0.0.1",
                  "peercert": "nossl",
                  "port": 60491,
                  "proto_name": "MQTT",
                  "proto_ver": 4,
                  "username": "undefined",
                  "will_topic": "undefined",
                  "zone": "external"
            }
      ],
      "meta": {
            "count": 2,
            "limit": 10000,
            "page": 1
      }
    }





List all Connections on a Node
--------------------------------



Definition::

    GET api/v3/nodes/${node}/connections/


Example Request::

    GET api/v3/nodes/emqx@127.0.0.1/connections/?_page=1&_limit=10000


Response:

.. code-block:: json

    {
      "items": [
            {
                  "clean_start": true,
                  "client_id": "emqx-api-test:v1",
                  "connected_at": "2018-09-14 10:23:04",
                  "ipaddress": "127.0.0.1",
                  "is_bridge": false,
                  "is_super": false,
                  "keepalive": 60,
                  "mountpoint": "undefined",
                  "node": "emqx@127.0.0.1",
                  "peercert": "nossl",
                  "port": 60492,
                  "proto_name": "MQTT",
                  "proto_ver": 4,
                  "username": "emqx-api-test:v1",
                  "will_topic": "undefined",
                  "zone": "external"
            },
            {
                  "clean_start": true,
                  "client_id": "mqttjs_68980a5d",
                  "connected_at": "2018-09-14 10:23:04",
                  "ipaddress": "127.0.0.1",
                  "is_bridge": false,
                  "is_super": false,
                  "keepalive": 60,
                  "mountpoint": "undefined",
                  "node": "emqx@127.0.0.1",
                  "peercert": "nossl",
                  "port": 60491,
                  "proto_name": "MQTT",
                  "proto_ver": 4,
                  "username": "undefined",
                  "will_topic": "undefined",
                  "zone": "external"
            }
      ],
      "meta": {
            "count": 2,
            "limit": 10000,
            "page": 1
      }
    }






Retrieve a Connection in the Cluster
-------------------------------------



Definition::

    GET api/v3/connections/${clientid}


Example Request::

    GET api/v3/connections/emqx-api-test:v1


Response:

.. code-block:: json

    [
      {
            "clean_start": true,
            "client_id": "emqx-api-test:v1",
            "connected_at": "2018-09-14 10:23:04",
            "ipaddress": "127.0.0.1",
            "is_bridge": false,
            "is_super": false,
            "keepalive": 60,
            "mountpoint": "undefined",
            "node": "emqx@127.0.0.1",
            "peercert": "nossl",
            "port": 60492,
            "proto_name": "MQTT",
            "proto_ver": 4,
            "username": "emqx-api-test:v1",
            "will_topic": "undefined",
            "zone": "external"
      }
    ]





Retrieve a Connection on a Node
--------------------------------



Definition::

    GET api/v3/nodes/${node}/connections/${clientid}


Example Request::

    GET api/v3/nodes/emqx@127.0.0.1/connections/emqx-api-test:v1


Response:

.. code-block:: json

    [
      {
            "clean_start": true,
            "client_id": "emqx-api-test:v1",
            "connected_at": "2018-09-14 10:23:04",
            "ipaddress": "127.0.0.1",
            "is_bridge": false,
            "is_super": false,
            "keepalive": 60,
            "mountpoint": "undefined",
            "node": "emqx@127.0.0.1",
            "peercert": "nossl",
            "port": 60492,
            "proto_name": "MQTT",
            "proto_ver": 4,
            "username": "emqx-api-test:v1",
            "will_topic": "undefined",
            "zone": "external"
      }
    ]






Kickout a Specified Connection of Cluster
----------------------------------------------



Definition::

    DELETE api/v3/connections/${clientid}


Example Request::

    DELETE api/v3/connections/emqx-api-test:v1


Response:

.. code-block:: json

"ok"






---------
Sessions
---------

List all Sessions in the Cluster
---------------------------------



Definition::

    GET api/v3/sessions/


Example Request::

    GET api/v3/sessions/?_page=1&_limit=10000


Response:

.. code-block:: json

    {
      "items": [
            {
                  "awaiting_rel_len": 0,
                  "binding": "local",
                  "clean_start": true,
                  "client_id": "emqx-api-test:v1",
                  "created_at": "2018-09-14 10:23:04",
                  "deliver_msg": 0,
                  "enqueue_msg": 0,
                  "expiry_interval": 7200,
                  "heap_size": 376,
                  "inflight_len": 0,
                  "mailbox_len": 0,
                  "max_awaiting_rel": 100,
                  "max_inflight": 32,
                  "max_mqueue": 1000,
                  "max_subscriptions": 0,
                  "mqueue_dropped": 0,
                  "mqueue_len": 0,
                  "node": "emqx@127.0.0.1",
                  "reductions": 203,
                  "subscriptions_count": 0,
                  "username": "emqx-api-test:v1"
            },
            {
                  "awaiting_rel_len": 0,
                  "binding": "local",
                  "clean_start": true,
                  "client_id": "mqttjs_68980a5d",
                  "created_at": "2018-09-14 10:23:04",
                  "deliver_msg": 0,
                  "enqueue_msg": 0,
                  "expiry_interval": 7200,
                  "heap_size": 233,
                  "inflight_len": 0,
                  "mailbox_len": 0,
                  "max_awaiting_rel": 100,
                  "max_inflight": 32,
                  "max_mqueue": 1000,
                  "max_subscriptions": 0,
                  "mqueue_dropped": 0,
                  "mqueue_len": 0,
                  "node": "emqx@127.0.0.1",
                  "reductions": 188,
                  "subscriptions_count": 0,
                  "username": "undefined"
            }
      ],
      "meta": {
            "count": 2,
            "limit": 10000,
            "page": 1
      }
    }





Retrieve a Session in the Cluster
----------------------------------



Definition::

    GET api/v3/sessions/${clientid}


Example Request::

    GET api/v3/sessions/emqx-api-test:v1


Response:

.. code-block:: json

    [
      {
            "awaiting_rel_len": 0,
            "binding": "local",
            "clean_start": true,
            "client_id": "emqx-api-test:v1",
            "created_at": "2018-09-14 10:23:04",
            "deliver_msg": 0,
            "enqueue_msg": 0,
            "expiry_interval": 7200,
            "heap_size": 376,
            "inflight_len": 0,
            "mailbox_len": 0,
            "max_awaiting_rel": 100,
            "max_inflight": 32,
            "max_mqueue": 1000,
            "max_subscriptions": 0,
            "mqueue_dropped": 0,
            "mqueue_len": 0,
            "node": "emqx@127.0.0.1",
            "reductions": 203,
            "subscriptions_count": 0,
            "username": "emqx-api-test:v1"
      }
    ]





List all Sessions on a Node
----------------------------



Definition::

    GET api/v3/nodes/${node}/sessions/


Example Request::

    GET api/v3/nodes/emqx@127.0.0.1/sessions/?_page=1&_limit=10000


Response:

.. code-block:: json

    {
      "items": [
            {
                  "awaiting_rel_len": 0,
                  "binding": "local",
                  "clean_start": true,
                  "client_id": "emqx-api-test:v1",
                  "created_at": "2018-09-14 10:23:04",
                  "deliver_msg": 0,
                  "enqueue_msg": 0,
                  "expiry_interval": 7200,
                  "heap_size": 376,
                  "inflight_len": 0,
                  "mailbox_len": 0,
                  "max_awaiting_rel": 100,
                  "max_inflight": 32,
                  "max_mqueue": 1000,
                  "max_subscriptions": 0,
                  "mqueue_dropped": 0,
                  "mqueue_len": 0,
                  "node": "emqx@127.0.0.1",
                  "reductions": 203,
                  "subscriptions_count": 0,
                  "username": "emqx-api-test:v1"
            },
            {
                  "awaiting_rel_len": 0,
                  "binding": "local",
                  "clean_start": true,
                  "client_id": "mqttjs_68980a5d",
                  "created_at": "2018-09-14 10:23:04",
                  "deliver_msg": 0,
                  "enqueue_msg": 0,
                  "expiry_interval": 7200,
                  "heap_size": 233,
                  "inflight_len": 0,
                  "mailbox_len": 0,
                  "max_awaiting_rel": 100,
                  "max_inflight": 32,
                  "max_mqueue": 1000,
                  "max_subscriptions": 0,
                  "mqueue_dropped": 0,
                  "mqueue_len": 0,
                  "node": "emqx@127.0.0.1",
                  "reductions": 188,
                  "subscriptions_count": 0,
                  "username": "undefined"
            }
      ],
      "meta": {
            "count": 2,
            "limit": 10000,
            "page": 1
      }
    }






Retrieve a Session on a Node
------------------------------



Definition::

    GET api/v3/nodes/${node}/sessions/${clientid}


Example Request::

    GET api/v3/nodes/emqx@127.0.0.1/sessions/emqx-api-test:v1


Response:

.. code-block:: json

    [
      {
            "awaiting_rel_len": 0,
            "binding": "local",
            "clean_start": true,
            "client_id": "emqx-api-test:v1",
            "created_at": "2018-09-14 10:23:04",
            "deliver_msg": 0,
            "enqueue_msg": 0,
            "expiry_interval": 7200,
            "heap_size": 376,
            "inflight_len": 0,
            "mailbox_len": 0,
            "max_awaiting_rel": 100,
            "max_inflight": 32,
            "max_mqueue": 1000,
            "max_subscriptions": 0,
            "mqueue_dropped": 0,
            "mqueue_len": 0,
            "node": "emqx@127.0.0.1",
            "reductions": 203,
            "subscriptions_count": 0,
            "username": "emqx-api-test:v1"
      }
    ]







--------------
Subscriptions
--------------


List all Subscriptions in the Cluster
--------------------------------------



Definition::

    GET api/v3/subscriptions/


Example Request::

    GET api/v3/subscriptions/?_page=1&_limit=10000


Response:

.. code-block:: json

    {
      "items": [
            {
                  "client_id": "emqx-api-test:v1",
                  "node": "emqx@127.0.0.1",
                  "qos": 0,
                  "topic": "/test"
            },
            {
                  "client_id": "mqttjs_68980a5d",
                  "node": "emqx@127.0.0.1",
                  "qos": 0,
                  "topic": "/test"
            }
      ],
      "meta": {
            "count": 2,
            "limit": 10000,
            "page": 1
      }
    }





List Subscriptions of a Connection in the Cluster
--------------------------------------------------



Definition::

    GET api/v3/subscriptions/${clientid}


Example Request::

    GET api/v3/subscriptions/emqx-api-test:v1


Response:

.. code-block:: json

    [
      {
            "client_id": "emqx-api-test:v1",
            "node": "emqx@127.0.0.1",
            "qos": 0,
            "topic": "/test"
      }
    ]





List all Subscriptions of a Node
---------------------------------



Definition::

    GET api/v3/nodes/${node}/subscriptions/


Example Request::

    GET api/v3/nodes/emqx@127.0.0.1/subscriptions/?_page=1&_limit=10000


Response:

.. code-block:: json

    {
      "items": [
            {
                  "client_id": "emqx-api-test:v1",
                  "node": "emqx@127.0.0.1",
                  "qos": 0,
                  "topic": "/test"
            },
            {
                  "client_id": "mqttjs_68980a5d",
                  "node": "emqx@127.0.0.1",
                  "qos": 0,
                  "topic": "/test"
            }
      ],
      "meta": {
            "count": 2,
            "limit": 10000,
            "page": 1
      }
    }




List Subscriptions of a Client on a node
-----------------------------------------


Definition::

    GET api/v3/nodes/${node}/subscriptions/${clientid}


Example Request::

    GET api/v3/nodes/emqx@127.0.0.1/subscriptions/emqx-api-test:v1


Response:

.. code-block:: json

    [
      {
            "client_id": "emqx-api-test:v1",
            "node": "emqx@127.0.0.1",
            "qos": 0,
            "topic": "/test"
      }
    ]




-------
Routes
-------

List all Routes in the Cluster
-------------------------------



Definition::

    GET api/v3/nodes/


Example Request::

    GET api/v3/nodes/


Response:

.. code-block:: json

    [
      {
            "connections": 2,
            "load1": "2.50",
            "load15": "2.09",
            "load5": "2.23",
            "max_fds": 7168,
            "memory_total": "77.45M",
            "memory_used": "59.81M",
            "name": "emqx@127.0.0.1",
            "node": "emqx@127.0.0.1",
            "node_status": "Running",
            "otp_release": "R21/10.0.5",
            "process_available": 262144,
            "process_used": 331,
            "uptime": "3 days,18 hours, 25 minutes, 11 seconds",
            "version": "3.0"
      }
    ]





Retrieve a Route of Topic in the Cluster
-----------------------------------------



Definition::

    GET api/v3/routes/${topic}


Example Request::

    GET api/v3/routes//test


Response:

.. code-block:: json

    []






------------------
Publish/Subscribe
------------------

Publish Message
----------------



Definition::

    POST api/v3/mqtt/publish

Request JSON Parameter:

.. code-block:: json

    {
      "topic": "test_topic",
      "payload": "hello",
      "qos": 1,
      "retain": false,
      "client_id": "mqttjs_ab9069449e"
    }

      

Example Request::

    POST api/v3/mqtt/publish


Response:

.. code-block:: json

    {
      "code": 0
    }




.. NOTE:: The topic parameter is required, other parameters are optional. Payload defaults to empty string, qos defaults to 0, retain defaults to false, client_id defaults to 'http'.

Create a Subscription
----------------------



Definition::

    POST api/v3/mqtt/subscribe

Request JSON Parameter:

.. code-block:: json

    {
      "topic": "test_topic",
      "qos": 1,
      "client_id": "mqttjs_ab9069449e"
    }

      

Example Request::

    POST api/v3/mqtt/subscribe


Response:

.. code-block:: json

    {
      "code": 112
    }





Unsubscribe Topic
------------------



Definition::

    POST api/v3/mqtt/unsubscribe

Request JSON Parameter:

.. code-block:: json

    {
      "topic": "test_topic",
      "payload": "hello",
      "qos": 1,
      "retain": false,
      "client_id": "mqttjs_ab9069449e"
    }

      

Example Request::

    POST api/v3/mqtt/unsubscribe


Response:

.. code-block:: json

    {
      "code": 112
    }




--------
Plugins
--------

List all Plugins of Cluster
--------------------------------



Definition::

    GET api/v3/plugins/


Example Request::

    GET api/v3/plugins/


Response:

.. code-block:: json

    [
      {
            "node": "emqx@127.0.0.1",
            "plugins": [
                  {
                        "name": "emqx_auth_clientid",
                        "version": "3.0",
                        "description": "EMQ X Authentication with ClientId/Password",
                        "active": false
                  },
                  {
                        "name": "emqx_auth_http",
                        "version": "3.0",
                        "description": "EMQ X Authentication/ACL with HTTP API",
                        "active": false
                  },
                  {
                        "name": "emqx_auth_jwt",
                        "version": "3.0",
                        "description": "EMQ X Authentication with JWT",
                        "active": false
                  },
                  {
                        "name": "emqx_auth_ldap",
                        "version": "3.0",
                        "description": "EMQ X Authentication/ACL with LDAP",
                        "active": false
                  },
                  {
                        "name": "emqx_auth_mongo",
                        "version": "3.0",
                        "description": "EMQ X Authentication/ACL with MongoDB",
                        "active": false
                  },
                  {
                        "name": "emqx_auth_mysql",
                        "version": "3.0",
                        "description": "EMQ X Authentication/ACL with MySQL",
                        "active": false
                  },
                  {
                        "name": "emqx_auth_pgsql",
                        "version": "3.0",
                        "description": "EMQ X Authentication/ACL with PostgreSQL",
                        "active": false
                  },
                  {
                        "name": "emqx_auth_redis",
                        "version": "3.0",
                        "description": "EMQ X Authentication/ACL with Redis",
                        "active": false
                  },
                  {
                        "name": "emqx_auth_username",
                        "version": "3.0",
                        "description": "EMQ X Authentication with Username/Password",
                        "active": false
                  },
                  {
                        "name": "emqx_coap",
                        "version": "3.0",
                        "description": "EMQ X CoAP Gateway",
                        "active": false
                  },
                  {
                        "name": "emqx_dashboard",
                        "version": "3.0",
                        "description": "EMQ X Web Dashboard",
                        "active": true
                  },
                  {
                        "name": "emqx_delayed_publish",
                        "version": "3.0",
                        "description": "EMQ X Delayed Publish",
                        "active": true
                  },
                  {
                        "name": "emqx_lwm2m",
                        "version": "3.0",
                        "description": "EMQ X LwM2M Gateway",
                        "active": false
                  },
                  {
                        "name": "emqx_management",
                        "version": "3.0",
                        "description": "EMQ X Management API and CLI",
                        "active": true
                  },
                  {
                        "name": "emqx_plugin_template",
                        "version": "3.0",
                        "description": "EMQ X Plugin Template",
                        "active": false
                  },
                  {
                        "name": "emqx_recon",
                        "version": "3.0",
                        "description": "EMQ X Recon Plugin",
                        "active": true
                  },
                  {
                        "name": "emqx_reloader",
                        "version": "3.0",
                        "description": "EMQ X Reloader Plugin",
                        "active": false
                  },
                  {
                        "name": "emqx_retainer",
                        "version": "3.0",
                        "description": "EMQ X Retainer",
                        "active": true
                  },
                  {
                        "name": "emqx_sn",
                        "version": "3.0",
                        "description": "EMQ X MQTT-SN Gateway",
                        "active": false
                  },
                  {
                        "name": "emqx_statsd",
                        "version": "3.0",
                        "description": "Statsd for EMQ X",
                        "active": false
                  },
                  {
                        "name": "emqx_stomp",
                        "version": "3.0",
                        "description": "EMQ X Stomp Protocol Plugin",
                        "active": false
                  },
                  {
                        "name": "emqx_web_hook",
                        "version": "3.0",
                        "description": "EMQ X Webhook Plugin",
                        "active": false
                  }
            ]
      }
    ]





List all Plugins of a Node
---------------------------



Definition::

    GET api/v3/nodes/${node}/plugins/


Example Request::

    GET api/v3/nodes/emqx@127.0.0.1/plugins/


Response:

.. code-block:: json

    [
      {
            "name": "emqx_auth_clientid",
            "version": "3.0",
            "description": "EMQ X Authentication with ClientId/Password",
            "active": false
      },
      {
            "name": "emqx_auth_http",
            "version": "3.0",
            "description": "EMQ X Authentication/ACL with HTTP API",
            "active": false
      },
      {
            "name": "emqx_auth_jwt",
            "version": "3.0",
            "description": "EMQ X Authentication with JWT",
            "active": false
      },
      {
            "name": "emqx_auth_ldap",
            "version": "3.0",
            "description": "EMQ X Authentication/ACL with LDAP",
            "active": false
      },
      {
            "name": "emqx_auth_mongo",
            "version": "3.0",
            "description": "EMQ X Authentication/ACL with MongoDB",
            "active": false
      },
      {
            "name": "emqx_auth_mysql",
            "version": "3.0",
            "description": "EMQ X Authentication/ACL with MySQL",
            "active": false
      },
      {
            "name": "emqx_auth_pgsql",
            "version": "3.0",
            "description": "EMQ X Authentication/ACL with PostgreSQL",
            "active": false
      },
      {
            "name": "emqx_auth_redis",
            "version": "3.0",
            "description": "EMQ X Authentication/ACL with Redis",
            "active": false
      },
      {
            "name": "emqx_auth_username",
            "version": "3.0",
            "description": "EMQ X Authentication with Username/Password",
            "active": false
      },
      {
            "name": "emqx_coap",
            "version": "3.0",
            "description": "EMQ X CoAP Gateway",
            "active": false
      },
      {
            "name": "emqx_dashboard",
            "version": "3.0",
            "description": "EMQ X Web Dashboard",
            "active": true
      },
      {
            "name": "emqx_delayed_publish",
            "version": "3.0",
            "description": "EMQ X Delayed Publish",
            "active": true
      },
      {
            "name": "emqx_lwm2m",
            "version": "3.0",
            "description": "EMQ X LwM2M Gateway",
            "active": false
      },
      {
            "name": "emqx_management",
            "version": "3.0",
            "description": "EMQ X Management API and CLI",
            "active": true
      },
      {
            "name": "emqx_plugin_template",
            "version": "3.0",
            "description": "EMQ X Plugin Template",
            "active": false
      },
      {
            "name": "emqx_recon",
            "version": "3.0",
            "description": "EMQ X Recon Plugin",
            "active": true
      },
      {
            "name": "emqx_reloader",
            "version": "3.0",
            "description": "EMQ X Reloader Plugin",
            "active": false
      },
      {
            "name": "emqx_retainer",
            "version": "3.0",
            "description": "EMQ X Retainer",
            "active": true
      },
      {
            "name": "emqx_sn",
            "version": "3.0",
            "description": "EMQ X MQTT-SN Gateway",
            "active": false
      },
      {
            "name": "emqx_statsd",
            "version": "3.0",
            "description": "Statsd for EMQ X",
            "active": false
      },
      {
            "name": "emqx_stomp",
            "version": "3.0",
            "description": "EMQ X Stomp Protocol Plugin",
            "active": false
      },
      {
            "name": "emqx_web_hook",
            "version": "3.0",
            "description": "EMQ X Webhook Plugin",
            "active": false
      }
    ]





Start a Plugin
---------------



Definition::

    PUT api/v3/nodes/${node}/plugins/${plugin}/load


Example Request::

    PUT api/v3/nodes/emqx@127.0.0.1/plugins/emqx_auth_clientid/load


Response:

.. code-block:: json

"ok"





Start a Plugin
---------------



Definition::

    PUT api/v3/nodes/${node}/plugins/${plugin}/unload


Example Request::

    PUT api/v3/nodes/emqx@127.0.0.1/plugins/emqx_auth_clientid/unload


Response:

.. code-block:: json

"ok"





----------
Listeners
----------

List all Listeners of Cluster
----------------------------------



Definition::

    GET api/v3/listeners/


Example Request::

    GET api/v3/listeners/


Response:

.. code-block:: json

    [
      {
            "listeners": [
                  {
                        "acceptors": 16,
                        "current_conns": 0,
                        "listen_on": "8883",
                        "max_conns": 102400,
                        "protocol": "mqtt:ssl",
                        "shutdown_count": []
                  },
                  {
                        "acceptors": 8,
                        "current_conns": 2,
                        "listen_on": "0.0.0.0:1883",
                        "max_conns": 1024000,
                        "protocol": "mqtt:tcp",
                        "shutdown_count": {
                              "closed": 6,
                              "kicked": 3
                        }
                  },
                  {
                        "acceptors": 4,
                        "current_conns": 0,
                        "listen_on": "127.0.0.1:11883",
                        "max_conns": 10240000,
                        "protocol": "mqtt:tcp",
                        "shutdown_count": []
                  },
                  {
                        "acceptors": 4,
                        "current_conns": 1,
                        "listen_on": "18083",
                        "max_conns": 512,
                        "protocol": "http:dashboard",
                        "shutdown_count": []
                  },
                  {
                        "acceptors": 2,
                        "current_conns": 0,
                        "listen_on": "8080",
                        "max_conns": 512,
                        "protocol": "http:management",
                        "shutdown_count": []
                  },
                  {
                        "acceptors": 4,
                        "current_conns": 0,
                        "listen_on": "8083",
                        "max_conns": 102400,
                        "protocol": "mqtt:ws",
                        "shutdown_count": []
                  },
                  {
                        "acceptors": 4,
                        "current_conns": 0,
                        "listen_on": "8084",
                        "max_conns": 16,
                        "protocol": "mqtt:wss",
                        "shutdown_count": []
                  }
            ],
            "node": "emqx@127.0.0.1"
      }
    ]





ist all Listeners of a Node
----------------------------



Definition::

    GET api/v3/nodes/${node}/listeners


Example Request::

    GET api/v3/nodes/emqx@127.0.0.1/listeners


Response:

.. code-block:: json

    [
      {
            "acceptors": 16,
            "current_conns": 0,
            "listen_on": "8883",
            "max_conns": 102400,
            "protocol": "mqtt:ssl",
            "shutdown_count": []
      },
      {
            "acceptors": 8,
            "current_conns": 2,
            "listen_on": "0.0.0.0:1883",
            "max_conns": 1024000,
            "protocol": "mqtt:tcp",
            "shutdown_count": {
                  "closed": 6,
                  "kicked": 3
            }
      },
      {
            "acceptors": 4,
            "current_conns": 0,
            "listen_on": "127.0.0.1:11883",
            "max_conns": 10240000,
            "protocol": "mqtt:tcp",
            "shutdown_count": []
      },
      {
            "acceptors": 4,
            "current_conns": 1,
            "listen_on": "18083",
            "max_conns": 512,
            "protocol": "http:dashboard",
            "shutdown_count": []
      },
      {
            "acceptors": 2,
            "current_conns": 0,
            "listen_on": "8080",
            "max_conns": 512,
            "protocol": "http:management",
            "shutdown_count": []
      },
      {
            "acceptors": 4,
            "current_conns": 0,
            "listen_on": "8083",
            "max_conns": 102400,
            "protocol": "mqtt:ws",
            "shutdown_count": []
      },
      {
            "acceptors": 4,
            "current_conns": 0,
            "listen_on": "8084",
            "max_conns": 16,
            "protocol": "mqtt:wss",
            "shutdown_count": []
      }
    ]




---------------------------------------
Statistics of packet sent and received
---------------------------------------

Get Statistics in the Cluster
------------------------------



Definition::

    GET api/v3/metrics/


Example Request::

    GET api/v3/metrics/


Response:

.. code-block:: json

    [
      {
            "node": "emqx@127.0.0.1",
            "metrics": {
                  "bytes/received": 750,
                  "packets/pubrel/sent": 0,
                  "packets/pubcomp/missed": 0,
                  "packets/sent": 27,
                  "packets/pubrel/received": 0,
                  "messages/qos1/received": 0,
                  "packets/publish/received": 4,
                  "packets/auth": 0,
                  "messages/qos0/received": 4,
                  "packets/pubcomp/received": 0,
                  "packets/unsuback": 0,
                  "packets/pubrec/missed": 0,
                  "messages/qos1/sent": 0,
                  "messages/qos2/sent": 0,
                  "bytes/sent": 236,
                  "messages/received": 4,
                  "messages/dropped": 3,
                  "messages/qos2/received": 0,
                  "packets/connect": 11,
                  "messages/qos0/sent": 8,
                  "packets/disconnect/received": 0,
                  "packets/pubrec/sent": 0,
                  "packets/publish/sent": 8,
                  "packets/pubrec/received": 0,
                  "packets/received": 23,
                  "packets/unsubscribe": 0,
                  "packets/subscribe": 8,
                  "packets/disconnect/sent": 0,
                  "packets/pingresp": 0,
                  "messages/qos2/dropped": 0,
                  "packets/puback/missed": 0,
                  "packets/pingreq": 0,
                  "packets/connack": 11,
                  "packets/pubrel/missed": 0,
                  "messages/sent": 8,
                  "packets/suback": 8,
                  "messages/retained": 3,
                  "packets/puback/sent": 0,
                  "packets/puback/received": 0,
                  "messages/qos2/expired": 0,
                  "messages/forward": 0,
                  "messages/expired": 0,
                  "packets/pubcomp/sent": 0
            }
      }
    ]




Get Statistics of specified Node
---------------------------------



Definition::

    GET api/v3/nodes/${node}/metrics/


Example Request::

    GET api/v3/nodes/emqx@127.0.0.1/metrics/


Response:

.. code-block:: json

    {
      "bytes/received": 750,
      "packets/pubrel/sent": 0,
      "packets/pubcomp/missed": 0,
      "packets/sent": 27,
      "packets/pubrel/received": 0,
      "messages/qos1/received": 0,
      "packets/publish/received": 4,
      "packets/auth": 0,
      "messages/qos0/received": 4,
      "packets/pubcomp/received": 0,
      "packets/unsuback": 0,
      "packets/pubrec/missed": 0,
      "messages/qos1/sent": 0,
      "messages/qos2/sent": 0,
      "bytes/sent": 236,
      "messages/received": 4,
      "messages/dropped": 3,
      "messages/qos2/received": 0,
      "packets/connect": 11,
      "messages/qos0/sent": 8,
      "packets/disconnect/received": 0,
      "packets/pubrec/sent": 0,
      "packets/publish/sent": 8,
      "packets/pubrec/received": 0,
      "packets/received": 23,
      "packets/unsubscribe": 0,
      "packets/subscribe": 8,
      "packets/disconnect/sent": 0,
      "packets/pingresp": 0,
      "messages/qos2/dropped": 0,
      "packets/puback/missed": 0,
      "packets/pingreq": 0,
      "packets/connack": 11,
      "packets/pubrel/missed": 0,
      "messages/sent": 8,
      "packets/suback": 8,
      "messages/retained": 3,
      "packets/puback/sent": 0,
      "packets/puback/received": 0,
      "messages/qos2/expired": 0,
      "messages/forward": 0,
      "messages/expired": 0,
      "packets/pubcomp/sent": 0
    }





--------------------------------
Statistics of connected session
--------------------------------

Get Statistics of connected session of Cluster
---------------------------------------------------



Definition::

    GET api/v3/stats/


Example Request::

    GET api/v3/stats/


Response:

.. code-block:: json

    [
      {
            "node": "emqx@127.0.0.1",
            "subscriptions/shared/max": 0,
            "subscriptions/max": 2,
            "subscribers/max": 2,
            "topics/count": 0,
            "subscriptions/count": 0,
            "topics/max": 1,
            "sessions/persistent/max": 2,
            "connections/max": 2,
            "subscriptions/shared/count": 0,
            "sessions/persistent/count": 0,
            "retained/count": 3,
            "routes/count": 0,
            "sessions/count": 0,
            "retained/max": 3,
            "sessions/max": 2,
            "routes/max": 1,
            "subscribers/count": 0,
            "connections/count": 0
      }
    ]




Get Statistics of connected session on specified node
------------------------------------------------------



Definition::

    GET api/v3/nodes/${node}/stats/


Example Request::

    GET api/v3/nodes/emqx@127.0.0.1/stats/


Response:

.. code-block:: json

    {
      "subscriptions/shared/max": 0,
      "subscriptions/max": 2,
      "subscribers/max": 2,
      "topics/count": 0,
      "subscriptions/count": 0,
      "topics/max": 1,
      "sessions/persistent/max": 2,
      "connections/max": 2,
      "subscriptions/shared/count": 0,
      "sessions/persistent/count": 0,
      "retained/count": 3,
      "routes/count": 0,
      "sessions/count": 0,
      "retained/max": 3,
      "sessions/max": 2,
      "routes/max": 1,
      "subscribers/count": 0,
      "connections/count": 0
    }





------------------
Hot configuration
------------------

Get Modifiable configuration items of Cluster
--------------------------------------------------



Definition::

    GET api/v3/configs/


Example Request::

    GET api/v3/configs/


Response:

.. code-block:: json

    [
      {
            "config": [
                  {
                        "key": "retainer.expiry_interval",
                        "value": "0",
                        "datatpye": "integer, duration",
                        "app": "emqx_retainer"
                  },
                  {
                        "key": "retainer.max_payload_size",
                        "value": "1048576",
                        "datatpye": "bytesize",
                        "app": "emqx_retainer"
                  },
                  {
                        "key": "retainer.max_retained_messages",
                        "value": "0",
                        "datatpye": "integer",
                        "app": "emqx_retainer"
                  }
            ],
            "node": "emqx@127.0.0.1"
      }
    ]




Get Modifiable configuration items of specified node
-----------------------------------------------------



Definition::

    GET api/v3/nodes/${node}/configs/


Example Request::

    GET api/v3/nodes/${node}/configs/


Response:

.. code-block:: json

"ok"





Modify configuration items of Cluster
--------------------------------------



Definition::

    PUT api/v3/configs/:app

Request JSON Parameter:

.. code-block:: json

    {
      "key": "mqtt.allow_anonymous",
      "value": "false"
    }

      

Example Request::

    PUT api/v3/configs/:app


Response:

.. code-block:: json

"ok"





Modify configuration items of specified node
---------------------------------------------



Definition::

    PUT api/v3/nodes/${node}/configs/:app

Request JSON Parameter:

.. code-block:: json

    {
      "key": "mqtt.allow_anonymous",
      "value": "false"
    }

      

Example Request::

    PUT api/v3/nodes/${node}/configs/:app


Response:

.. code-block:: json

"ok"





--------
Alarms
--------

Get Modifiable alarms of Cluster
-------------------------------------



Definition::

    GET api/v3/alarms/${node}


Example Request::

    GET api/v3/alarms/emqx@127.0.0.1


Response:

.. code-block:: json

    []




Get Modifiable alarms of specified node
----------------------------------------



Definition::

    GET api/v3/alarms/


Example Request::

    GET api/v3/alarms/


Response:

.. code-block:: json

    [
      {
            "alarms": [],
            "node": "emqx@127.0.0.1"
      }
    ]






-------
Banned
-------

List all Banned of Cluster
------------------------------



Definition::

    GET api/v3/banned/


Example Request::

    GET api/v3/banned/?_page=1&_limit=10000


Response:

.. code-block:: json

    {
      "items": [
            {
                  "as": "client_id",
                  "by": "undefined",
                  "desc": "normal banned",
                  "reason": "banned the clientId",
                  "until": 1536146187,
                  "who": "clientId/username/ipAddress"
            }
      ],
      "meta": {
            "count": 1,
            "limit": 10000,
            "page": 1
      }
    }




Create a Banned
----------------



Definition::

    POST api/v3/banned/

Request JSON Parameter:

.. code-block:: json

    {
      "who": "clientId/username/ipAddress",
      "as": "client_id",
      "reason": "banned the clientId",
      "desc": "normal banned",
      "until": 1536146187
    }

      

Example Request::

    POST api/v3/banned/


Response:

.. code-block:: json

    {
      "who": "clientId/username/ipAddress",
      "as": "client_id",
      "reason": "banned the clientId",
      "desc": "normal banned",
      "until": 1536146187
    }




Delete a Banned
----------------



Definition::

    DELETE api/v3/banned/${who}?as=${as}


Example Request::

    DELETE api/v3/banned/${who}?as=${as}


Response:

.. code-block:: json

"ok"






-------------------------
Error Message/Pagination
-------------------------


When the HTTP status code is greater than 500, the response brings back the error message.
-----------------------------------------------------------------------------------

Example Request::

    PUT api/v3/nodes/emqx@127.0.0.1/plugins/emqx_recon/load

Response:

.. code-block:: json

    {
        "message": "already_started"
    }


Paging parameters and information
----------------------------------

The API that uses the _page=1&_limit=10000 parameter in the request example supports paging::

    _page: Current Page
    _limit: Page Size
    
    
Response:

.. code-block:: json    

    {
      "items": [],
      "meta": {
          "page": 1,
          "limit": 10000,
          "count": 2
      }
    }
    
