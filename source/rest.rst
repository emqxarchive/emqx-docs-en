
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

The HTTP requests to the REST API are protected with HTTP Basic authentication, For example:

.. code-block:: bash

    curl -v --basic -u <user>:<passwd> -k http://localhost:8080/api/v3/nodes/emqx@127.0.0.1/clients


-----
Nodes
-----

List all Nodes in the Cluster
-----------------------------

Definition::

    GET api/v3/management/nodes

Example Request::

    GET api/v3/management/nodes

Response:

.. code-block:: json

    {
    	"code": 0,
    	"result": [
    		{
    			"name": "emqx@127.0.0.1",
    			"version": "3.0",
    			"sysdescr": "Erlang MQTT Broker",
    			"uptime": "3 minutes, 32 seconds",
    			"datetime": "2018-06-29 09:03:52",
    			"otp_release": "R20/9.3.3",
    			"node_status": "Running"
    		}
    	]
    }

Retrieve a Node's Info
----------------------

Definition::

    GET api/v3/management/nodes/{node_name}

Example Request::

    GET api/v3/management/nodes/emqx@127.0.0.1
 
Response:

.. code-block:: json

    {
    	"code": 0,
    	"result": {
    		"version": "3.0",
    		"sysdescr": "Erlang MQTT Broker",
    		"uptime": "5 minutes, 12 seconds",
    		"datetime": "2018-06-29 09:05:32",
    		"otp_release": "R20/9.3.3",
    		"node_status": "Running"
    	}
    }

List all Nodes'statistics in the Cluster
----------------------------------------

Definition::

    GET api/v3/monitoring/nodes

Example Request::

    GET api/v3/monitoring/nodes

Response:

.. code-block:: json

    {
    	"code": 0,
    	"result": [
    		{
    			"name": "emqx@127.0.0.1",
    			"otp_release": "R20/9.3.3",
    			"memory_total": "72.94M",
    			"memory_used": "50.55M",
    			"process_available": 262144,
    			"process_used": 324,
    			"max_fds": 7168,
    			"clients": 0,
    			"node_status": "Running",
    			"load1": "1.65",
    			"load5": "1.93",
    			"load15": "2.01"
    		}
    	]
    }

Retrieve a node's statistics
---------------------------

Definition::

    GET api/v3/monitoring/nodes/{node_name}

Example Request::

    GET api/v3/monitoring/nodes/emqx@127.0.0.1

Response:

.. code-block:: json

    {
    	"code": 0,
    	"result": {
    		"name": "emqx@127.0.0.1",
    		"otp_release": "R20/9.3.3",
    		"memory_total": "73.69M",
    		"memory_used": "50.12M",
    		"process_available": 262144,
    		"process_used": 324,
    		"max_fds": 7168,
    		"clients": 0,
    		"node_status": "Running",
    		"load1": "1.88",
    		"load5": "1.99",
    		"load15": "2.02"
    	}
    }

-------
Clients
-------

List all Clients on a Node
--------------------------

Definition::

    GET api/v3/nodes/{node_name}/clients

Request Parameter::

    curr_page={page_no}&page_size={page_size}

Example Request::

    api/v3/nodes/emqx@127.0.0.1/clients?curr_page=1&page_size=20

Response:

.. code-block:: json

    {
    	"code": 0,
    	"result": {
    		"current_page": 1,
    		"page_size": 20,
    		"total_num": 1,
    		"total_page": 1,
    		"objects": [
    			{
    				"client_id": "mqttjs_722b4d845f",
    				"username": "undefined",
    				"ipaddress": "127.0.0.1",
    				"port": 58459,
    				"clean_sess": true,
    				"proto_ver": 4,
    				"keepalive": 60,
    				"connected_at": "2018-06-29 09:15:25"
    			}
    		]
    	}
    }

Retrieve a Client on a Node
--------------------------

Definition::

    GET api/v3/nodes/{node_name}/clients/{client_id}

Example Request::

    GET api/v3/nodes/emqx@127.0.0.1/clients/mqttjs_722b4d845f

Response:

.. code-block:: json

    {
    	"code": 0,
    	"result": {
    		"objects": [
    			{
    				"client_id": "mqttjs_722b4d845f",
    				"username": "undefined",
    				"ipaddress": "127.0.0.1",
    				"port": 58459,
    				"clean_sess": true,
    				"proto_ver": 4,
    				"keepalive": 60,
    				"connected_at": "2018-06-29 09:15:25"
    			}
    		]
    	}
    }

Retrieve a Client in the Cluster
-------------------------------

Definition::

    GET api/v3/clients/{client_id}

Example Request::

    GET api/v3/clients/mqttjs_722b4d845f

Response:

.. code-block:: json

    {
    	"code": 0,
    	"result": {
    		"objects": [
    			{
    				"client_id": "mqttjs_722b4d845f",
    				"username": "undefined",
    				"ipaddress": "127.0.0.1",
    				"port": 58459,
    				"clean_sess": true,
    				"proto_ver": 4,
    				"keepalive": 60,
    				"connected_at": "2018-06-29 09:15:25"
    			}
    		]
    	}
    }

Disconnect a Specified Client in the Cluster 
--------------------------------------------

Definition::

    DELETE api/v3/clients/{clientid}

Example Request::

    DELETE api/v3/clients/mqttjs_722b4d845f

Response:

.. code-block:: json

    {
        "code": 0,
        "result": []
    }

Clear the ACL of a Specified Client in the Cluster
--------------------------------------------------

Definition::

    PUT api/v3/clients/{clientid}/clean_acl_cache

Request Parameter:

.. code-block:: json

    {
        "topic": "test"
    }

Request Example::

    PUT api/v3/clients/C_1492145414740/clean_acl_cache

    Request Json Parameter:
    {
        "topic": "test"
    }

Response:

.. code-block:: json

    {
        "code": 0,
        "result": []
    }

--------
Sessions
--------

List all Sessions on a Node
---------------------------

Definition::

    GET api/v3/node/{node_name}/sessions

Request Parameter::

    curr_page={page_no}&page_size={page_size}

Example Request::

    GET api/v3/nodes/emqx@127.0.0.1/sessions?curr_page=1&page_size=20

Response:

.. code-block:: json

    {
    	"code": 0,
    	"result": {
    		"current_page": 1,
    		"page_size": 20,
    		"total_num": 1,
    		"total_page": 1,
    		"objects": [
    			{
    				"client_id": "mqttjs_722b4d845f",
    				"clean_sess": true,
    				"subscriptions": 0,
    				"max_inflight": 32,
    				"inflight_len": 0,
    				"mqueue_len": 0,
    				"mqueue_dropped": 0,
    				"awaiting_rel_len": 0,
    				"deliver_msg": 0,
    				"enqueue_msg": 0,
    				"created_at": "2018-06-29 10:05:13"
    			}
    		]
    	}
    }
    
Retrieve a Session on a Node
----------------------------

Definition::

    GET api/v3/nodes/{node_name}/sessions/{client_id}

Example Request::

    GET api/v3/nodes/emqx@127.0.0.1/sessions/mqttjs_722b4d845f

Response:

.. code-block:: json

    {
    	"code": 0,
    	"result": {
    		"objects": [
    			{
    				"client_id": "mqttjs_722b4d845f",
    				"clean_sess": true,
    				"subscriptions": 0,
    				"max_inflight": 32,
    				"inflight_len": 0,
    				"mqueue_len": 0,
    				"mqueue_dropped": 0,
    				"awaiting_rel_len": 0,
    				"deliver_msg": 0,
    				"enqueue_msg": 0,
    				"created_at": "2018-06-29 10:05:13"
    			}
    		]
    	}
    }

Retrieve a Session in the Cluster
--------------------------------

Definition::

    GET api/v3/sessions/{client_id}

Example Request::

    GET api/v3/sessions/mqttjs_722b4d845f

Response:

.. code-block:: json

    {
    	"code": 0,
    	"result": {
    		"objects": [
    			{
    				"client_id": "mqttjs_722b4d845f",
    				"clean_sess": true,
    				"subscriptions": 0,
    				"max_inflight": 32,
    				"inflight_len": 0,
    				"mqueue_len": 0,
    				"mqueue_dropped": 0,
    				"awaiting_rel_len": 0,
    				"deliver_msg": 0,
    				"enqueue_msg": 0,
    				"created_at": "2018-06-29 10:05:13"
    			}
    		]
    	}
    }
    
-------------
Subscriptions
-------------

List all Subscriptions of a Node
--------------------------------

Definition::

    GET api/v3/nodes/{node_name}/subscriptions
    
Request parameters::

    curr_page={page_no}&page_size={page_size}

Example Request::

    GET api/v3/nodes/emqx@127.0.0.1/subscriptions?curr_page=1&page_size=20

Response:

.. code-block:: json

    {
    	"code": 0,
    	"result": {
    		"current_page": 1,
    		"page_size": 20,
    		"total_num": 1,
    		"total_page": 1,
    		"objects": [
    			{
    				"client_id": "mqttjs_722b4d845f",
    				"topic": "/World",
    				"qos": 0
    			}
    		]
    	}
    }
    
List Subscriptions of a Client on a node
------------------------------

Definition::

    GET api/v3/nodes/{node_name}/subscriptions/{clientid}

Example Request::

    GET api/v3/nodes/emqx@127.0.0.1/subscriptions/mqttjs_722b4d845f

Response:

.. code-block:: json

    {
    	"code": 0,
    	"result": {
    		"objects": [
    			{
    				"client_id": "mqttjs_722b4d845f",
    				"topic": "/World",
    				"qos": 0
    			}
    		]
    	}
    }

List Subscriptions of a Client in cluster
-----------------------------------------

Definition::

    GET api/v3/subscriptions/{clientid}

Example Request::

    GET api/v3/subscriptions/mqttjs_722b4d845f

Response:

.. code-block:: json

    {
    	"code": 0,
    	"result": {
    		"objects": [
    			{
    				"client_id": "mqttjs_722b4d845f",
    				"topic": "/World",
    				"qos": 0
    			}
    		]
    	}
    }

------
Routes
------

List all Routes in the Cluster
-------------------------------

Definition::

    GET api/v3/routes

Request parameters::

    curr_page={page_no}&page_size={page_size}

Example Request::
    
    GET api/v3/routes?curr_page=1&page_size=20

Response:

.. code-block:: json

    {
    	"code": 0,
    	"result": {
    		"current_page": 1,
    		"page_size": 20,
    		"total_num": 1,
    		"total_page": 1,
    		"objects": [
    			{
    				"topic": "/World",
    				"node": "emqx@127.0.0.1"
    			}
    		]
    	}
    }

Retrieve a Route of Topic in the Cluster
-------------------------------

Definition::

    GET api/v3/routes/{topic}

Example Request::

    GET api/v3/routes//World

Response:

.. code-block:: json

    {
    	"code": 0,
    	"result": {
    		"objects": [
    			{
    				"topic": "/World",
    				"node": "emqx@127.0.0.1"
    			}
    		]
    	}
    }

------------------
Publish/Subscribe
------------------

Publish Message
------------------

Definition::
 
    POST api/v3/mqtt/publish

Request parameters:

.. code-block:: json

    {
    	"topic" : "/World",
    	"payload": "hello",
    	"qos": 0,
    	"retain" : false,
    	"client_id": "mqttjs_722b4d845f"
    }
 
.. NOTE:: The topic parameter is required, other parameters are optional. Payload defaults to empty string, qos defaults to 0, retain defaults to false, client_id defaults to 'http'.

Example Request::

    POST api/v3/mqtt/publish

    Request Json Parameter:
    {
	      "topic" : "/World",
        "payload": "hello",
	      "qos": 0,
	      "retain" : false,
    	  "client_id": "mqttjs_722b4d845f"
    }

Response:
  
.. code-block:: json

    {
        "code": 0,
        "result": []
    }

Create a Subscription
----------------------

Definition::

    POST api/v3/mqtt/subscribe

Request parameters:

.. code-block:: json

    {
        "topic": "/World",
        "qos": 0,
        "client_id": "mqttjs_722b4d845f"
    }

Example Request::
 
    POST api/v3/mqtt/subscribe
    Request Json Parameter:
    {
	      "topic" : "/World",
	      "qos": 0,
    	  "client_id": "mqttjs_722b4d845f"
    }

Response:

.. code-block:: json

    {
        "code": 0,
        "result": []
    }

Unsubscribe Topic
------------

Definition::

    POST api/v3/mqtt/unsubscribe

Request Parameter:

.. code-block:: json

    {
	      "topic" : "/World",
    	  "client_id": "mqttjs_722b4d845f"
    }

Example Request::

    POST api/v3/mqtt/unsubscribe
    Request Json Parameter:
    {
	      "topic" : "/World",
    	  "client_id": "mqttjs_722b4d845f"
    }

Response:

.. code-block:: json

    {
        "code": 0,
        "result": []
    }

-------
Plugins
-------

List all Plugins of a Node
--------------------------

Definition::

    GET /api/v3/nodes/{node_name}/plugins/

Example Request::

    GET api/v3/nodes/emqx@127.0.0.1/plugins

Response:

.. code-block:: json

    {
    	"code": 0,
    	"result": [
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
    			"name": "emqx_lua_hook",
    			"version": "3.0",
    			"description": "EMQ X Hooks in lua",
    			"active": false
    		},
    		{
    			"name": "emqx_modules",
    			"version": "3.0",
    			"description": "EMQ X Modules",
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

Start/Stop a Plugin
-------------------

Definition::

    PUT /api/v3/nodes/{node_name}/plugins/{name}

Request parameters:

.. code-block:: json 

    {
        "active": true/false,
    }

Example Request::

    PUT api/v3/nodes/emqx@127.0.0.1/plugins/emqx_recon
    Request Json Parameter:
    {
    	"active": true
    }

Response:

.. code-block:: json

    {
        "code": 0,
        "result": []
    }

List all Listeners
------------------

Definition::

    GET api/v3/monitoring/listeners

Response:

.. code-block:: json

    {
        "code": 0,
        "result": {
            "emqx@127.0.0.1": [
                {
                    "protocol": "dashboard:http",
                    "listen": "18083",
                    "acceptors": 2,
                    "max_clients": 512,
                    "current_clients": 0,
                    "shutdown_count": []
                },
                {
                    "protocol": "mqtt:tcp",
                    "listen": "127.0.0.1:11883",
                    "acceptors": 16,
                    "max_clients": 102400,
                    "current_clients": 0,
                    "shutdown_count": []
                },
                {
                    "protocol": "mqtt:tcp",
                    "listen": "0.0.0.0:1883",
                    "acceptors": 16,
                    "max_clients": 102400,
                    "current_clients": 0,
                    "shutdown_count": []
                },
                {
                    "protocol": "mqtt:ws",
                    "listen": "8083",
                    "acceptors": 4,
                    "max_clients": 64,
                    "current_clients": 0,
                    "shutdown_count": []
                },
                {
                    "protocol": "mqtt:ssl",
                    "listen": "8883",
                    "acceptors": 16,
                    "max_clients": 1024,
                    "current_clients": 0,
                    "shutdown_count": []
                },
                {
                    "protocol": "mqtt:wss",
                    "listen": "8084",
                    "acceptors": 4,
                    "max_clients": 64,
                    "current_clients": 0,
                    "shutdown_count": []
                },
                {
                    "protocol": "mqtt:api",
                    "listen": "127.0.0.1:8080",
                    "acceptors": 4,
                    "max_clients": 64,
                    "current_clients": 1,
                    "shutdown_count": []
                }
            ]
        }
    }
    
List listeners of a Node
------------------------

Definition::

    GET api/v3/monitoring/listeners/{node_name}

Example Request::

    GET api/v3/monitoring/listeners/emqx@127.0.0.1
    
Response:

.. code-block:: json

    {
        "code": 0,
        "result": [
            {
                "protocol": "mqtt:api",
                "listen": "127.0.0.1:8080",
                "acceptors": 4,
                "max_clients": 64,
                "current_clients": 1,
                "shutdown_count": []
            },
            {
                "protocol": "mqtt:wss",
                "listen": "8084",
                "acceptors": 4,
                "max_clients": 64,
                "current_clients": 0,
                "shutdown_count": []
            },
            {
                "protocol": "mqtt:ssl",
                "listen": "8883",
                "acceptors": 16,
                "max_clients": 1024,
                "current_clients": 0,
                "shutdown_count": []
            },
            {
                "protocol": "mqtt:ws",
                "listen": "8083",
                "acceptors": 4,
                "max_clients": 64,
                "current_clients": 0,
                "shutdown_count": []
            },
            {
                "protocol": "mqtt:tcp",
                "listen": "0.0.0.0:1883",
                "acceptors": 16,
                "max_clients": 102400,
                "current_clients": 0,
                "shutdown_count": []
            },
            {
                "protocol": "mqtt:tcp",
                "listen": "127.0.0.1:11883",
                "acceptors": 16,
                "max_clients": 102400,
                "current_clients": 0,
                "shutdown_count": []
            },
            {
                "protocol": "dashboard:http",
                "listen": "18083",
                "acceptors": 2,
                "max_clients": 512,
                "current_clients": 0,
                "shutdown_count": []
            }
        ]
    }

-------------------------------------
Statistics of packet sent and received
-------------------------------------

Get Statistics of all Nodes
----------------------------

Definition::

    GET api/v3/monitoring/metrics/

Response:

.. code-block:: json

    {
        "code": 0,
        "result": {
            "packets/disconnect":0,
            "messages/dropped":0,
            "messages/qos2/received":0,
            "packets/suback":0,
            "packets/pubcomp/received":0,
            "packets/unsuback":0,
            "packets/pingresp":0,
            "packets/puback/missed":0,
            "packets/pingreq":0,
            "messages/retained":3,
            "packets/sent":0,
            "messages/qos2/dropped":0,
            "packets/unsubscribe":0,
            "packets/pubrec/missed":0,
            "packets/connack":0,
            "packets/pubrec/sent":0,
            "packets/publish/received":0,
            "packets/pubcomp/sent":0,
            "bytes/received":0,
            "packets/connect":0,
            "packets/puback/received":0,
            "messages/sent":0,
            "packets/publish/sent":0,
            "bytes/sent":0,
            "packets/pubrel/missed":0,
            "packets/puback/sent":0,
            "messages/qos0/received":0,
            "packets/subscribe":0,
            "packets/pubrel/sent":0,
            "messages/qos2/sent":0,
            "packets/received":0,
            "packets/pubrel/received":0,
            "messages/qos1/received":0,
            "messages/qos1/sent":0,
            "packets/pubrec/received":0,
            "packets/pubcomp/missed":0,
            "messages/qos0/sent":0
        }
    }

Get Statistics of specified Node
------------------------

Definition::

    GET api/v3/monitoring/metrics/{node_name}

Example Request::

    GET api/v3/monitoring/metrics/emqx@127.0.0.1

Response:

.. code-block:: json

    {
        "code": 0,
        "result": {
            "packets/disconnect":0,
            "messages/dropped":0,
            "messages/qos2/received":0,
            "packets/suback":0,
            "packets/pubcomp/received":0,
            "packets/unsuback":0,
            "packets/pingresp":0,
            "packets/puback/missed":0,
            "packets/pingreq":0,
            "messages/retained":3,
            "packets/sent":0,
            "messages/qos2/dropped":0,
            "packets/unsubscribe":0,
            "packets/pubrec/missed":0,
            "packets/connack":0,
            "messages/received":0,
            "packets/pubrec/sent":0,
            "packets/publish/received":0,
            "packets/pubcomp/sent":0,
            "bytes/received":0,
            "packets/connect":0,
            "packets/puback/received":0,
            "messages/sent":0,
            "packets/publish/sent":0,
            "bytes/sent":0,
            "packets/pubrel/missed":0,
            "packets/puback/sent":0,
            "messages/qos0/received":0,
            "packets/subscribe":0,
            "packets/pubrel/sent":0,
            "messages/qos2/sent":0,
            "packets/received":0,
            "packets/pubrel/received":0,
            "messages/qos1/received":0,
            "messages/qos1/sent":0,
            "packets/pubrec/received":0,
            "packets/pubcomp/missed":0,
            "messages/qos0/sent":0
        }
    }

--------------------------------
Statistics of connected session
--------------------------------

Get Statistics of connected session in all nodes
------------------------------------------------

Definition::

    GET api/v3/monitoring/stats

Example Request::

    GET api/v3/monitoring/stats

Response:

.. code-block:: json

    {
    	"code": 0,
    	"result": [
    		{
    			"emqx@127.0.0.1": {
    				"clients/count": 0,
    				"clients/max": 0,
    				"retained/count": 3,
    				"retained/max": 3,
    				"routes/count": 0,
    				"routes/max": 0,
    				"sessions/count": 0,
    				"sessions/max": 0,
    				"subscribers/count": 0,
    				"subscribers/max": 0,
    				"subscriptions/count": 0,
    				"subscriptions/max": 0,
    				"topics/count": 0,
    				"topics/max": 0
    			}
    		}
    	]
    }


Get Statistics of connected session on specified node
-----------------------------------------------------

Definition::

    GET api/v3/monitoring/stats/{node_name}

Example Request::

    GET api/v3/monitoring/stats/emqx@127.0.0.1

Response:

.. code-block:: json

   {
   	 "code": 0,
   	 "result": {
       "clients/count": 0,
       "clients/max": 0,
       "retained/count": 3,
       "retained/max": 3,
       "routes/count": 0,
       "routes/max": 0,
       "sessions/count": 0,
       "sessions/max": 0,
       "subscribers/count": 0,
       "subscribers/max": 0,
       "subscriptions/count": 0,
       "subscriptions/max": 0,
       "topics/count": 0,
       "topics/max": 0
   	 }
   }

-----------------
Hot configuration 
-----------------

Get Modifiable configuration items of all nodes
-----------------------------------------------

Definition::

    GET api/v3/configs

Example Request::

    GET api/v3/configs

Response:

.. code-block:: json

    {
        "code": 0,
        "result": {
            "emqx@127.0.0.1": [
                {
                    "key": "log.console.level",
                    "value": "error",
                    "datatpye": "enum",
                    "app": "emqx"
                },
                {
                    "key": "mqtt.acl_file",
                    "value": "etc/acl.conf",
                    "datatpye": "string",
                    "app": "emqx"
                },
                {
                    "key": "mqtt.acl_nomatch",
                    "value": "allow",
                    "datatpye": "enum",
                    "app": "emqx"
                },
                {
                    "key": "mqtt.allow_anonymous",
                    "value": "true",
                    "datatpye": "enum",
                    "app": "emqx"
                },
                {
                    "key": "mqtt.broker.sys_interval",
                    "value": "60",
                    "datatpye": "integer",
                    "app": "emqx"
                },
                {
                    "key": "mqtt.cache_acl",
                    "value": "true",
                    "datatpye": "enum",
                    "app": "emqx"
                }
            ]
        }
    }

Get Modifiable configuration items of specified node
----------------------------------------------------

Definition::

    GET api/v2/nodes/{node_name}/configs

Example Request::

    GET api/v3/nodes/emqx@127.0.0.1/configs

Response:

.. code-block:: json

    {
        "code": 0,
        "result": [
            {
                "key": "log.console.level",
                "value": "error",
                "datatpye": "enum",
                "app": "emqx"
            },
            {
                "key": "mqtt.acl_file",
                "value": "etc/acl.conf",
                "datatpye": "string",
                "app": "emqx"
            },
            {
                "key": "mqtt.acl_nomatch",
                "value": "allow",
                "datatpye": "enum",
                "app": "emqx"
            },
            {
                "key": "mqtt.allow_anonymous",
                "value": "true",
                "datatpye": "enum",
                "app": "emqx"
            },
            {
                "key": "mqtt.broker.sys_interval",
                "value": "60",
                "datatpye": "integer",
                "app": "emqx"
            },
            {
                "key": "mqtt.cache_acl",
                "value": "true",
                "datatpye": "enum",
                "app": "emqx"
            }
        ]
    }

Modify configuration items of all nodes
---------------------------------------

Definition::

    PUT /api/v3/configs/{app_name}

Request Parameter::

    {
        "key"   : "mqtt.allow_anonymous",
        "value" : "false"
    }

Example Request::

    PUT /api/v3/configs/emqx

Response:
.. code-block:: json

    {
        "code": 0,
        "result": []
    }

Modify configuration items of specified node
--------------------------------------------

Definition::

    PUT /api/v3/nodes/{node_name}/configs/{app_name}

Request Parameter::

    {
        "key"   : "mqtt.allow_anonymous",
        "value" : "false"
    }

Response:

.. code-block:: json

    {
        "code": 0,
        "result": []
    }

Get configuration items of specified plugin in specified node
-------------------------------------------------------------

Definition::

    GET api/v3/nodes/{node_name}/plugin_configs/{plugin_name}

Example Request::

    GET api/v3/nodes/emqx@127.0.0.1/plugin_configs/emqx_auth_http

Response:

.. code-block:: json

    {
    	"code": 0,
    	"result": [
    		{
    			"key": "auth.http.auth_req",
    			"value": "http://127.0.0.1:8080/mqtt/auth",
    			"desc": "",
    			"required": true
    		},
    		{
    			"key": "auth.http.auth_req.method",
    			"value": "post",
    			"desc": "",
    			"required": true
    		},
    		{
    			"key": "auth.http.auth_req.params",
    			"value": "clientid=%c,username=%u,password=%P",
    			"desc": "",
    			"required": true
    		},
    		{
    			"key": "auth.http.super_req",
    			"value": "http://127.0.0.1:8080/mqtt/superuser",
    			"desc": "",
    			"required": true
    		},
    		{
    			"key": "auth.http.super_req.method",
    			"value": "post",
    			"desc": "",
    			"required": true
    		},
    		{
    			"key": "auth.http.super_req.params",
    			"value": "clientid=%c,username=%u",
    			"desc": "",
    			"required": true
    		},
    		{
    			"key": "auth.http.acl_req",
    			"value": "http://127.0.0.1:8080/mqtt/acl",
    			"desc": "",
    			"required": true
    		},
    		{
    			"key": "auth.http.acl_req.method",
    			"value": "get",
    			"desc": "",
    			"required": true
    		},
    		{
    			"key": "auth.http.acl_req.params",
    			"value": "access=%A,username=%u,clientid=%c,ipaddr=%a,topic=%t",
    			"desc": "",
    			"required": true
    		}
    	]
    }

Modify configuration item of specified plugin in specified node
---------------------------------------------------------------

Definition::

    PUT api/v3/nodes/{node_name}/plugin_configs/{plugin_name}

Request Parameter::

    {
        "auth.http.auth_req.method": "get",
        "auth.http.auth_req": "http://127.0.0.1:8080/mqtt/auth",
        "auth.http.auth_req.params": "clientid=%c,username=%u,password=%P",
        "auth.http.acl_req.method": "get",
        "auth.http.acl_req": "http://127.0.0.1:8080/mqtt/acl",
        "auth.http.acl_req.params": "access=%A,username=%u,clientid=%c,ipaddr=%a,topic=%t",
        "auth.http.super_req.method": "post",
        "auth.http.super_req.params": "clientid=%c,username=%u",
        "auth.http.super_req": "http://127.0.0.1:8080/mqtt/superuser"
    }

Example Request::

    PUT api/v3/nodes/emqx@127.0.0.1/plugin_configs/emqx_auth_http

Response:

.. code-block:: json

    {
        "code": 0,
        "result": []
    }


---------------
User Management
---------------

Retrieve Admin User List
------------------------

Definition::

    GET api/v3/users

Request Example::

    GET api/v3/users

Response:

.. code-block:: json

    {
        "code": 0,
        "result": [
            {
                "username": "admin",
                "tags": "administrator"
            }
        ]
    }

Add Admin User
--------------

Definition::

    POST api/v3/users

Request Parameter::

    {
        "username": "test_user",
        "password": "password",
        "tags": "user"
    }

Request Example::

    POST api/v3/users

Response:

.. code-block:: json

    {
        "code": 0,
        "result": []
    }

Modify Admin User Information
-----------------------------

Definition::

    PUT api/v3/users/{username}

Request Parameter::

    {
        "tags": "admin"
    }

Request Example::

    PUT api/v3/users/test_user

Response:

.. code-block:: json

    {
        "code": 0,
        "result": []
    }

Delete Admin User
-----------------

Definition::

    DELETE api/v3/users/{username}

Request Parameter::


Request Example::

    DELETE api/v3/users/test_user

Response:

.. code-block:: json

    {
        "code": 0,
        "result": []
    }

Authenticate Admin User
-----------------------

Definition::

    POST api/v3/auth

Request Parameter::

    {
        "username": "test_user",
        "password": "password"
    }

Request Example::

    POST api/v3/auth

Response:

.. code-block:: json

    {
        "code": 0,
        "result": []
    }

Modify Admin User Password
--------------------------

Definition::

    PUT api/v3/change_pwd/{username}

Request Parameter::

    {
        "new_pwd": "newpassword",
        "old_pwd": "password"
    }

Request Example::

    PUT api/v3/change_pwd/test_user

Response:

.. code-block:: json

    {
        "code": 0,
        "result": []
    }

----------
Error Code
----------

+-------+-----------------------------------------+
| Code  | Comment                                 |
+=======+=========================================+
| 0     | Success                                 |
+-------+-----------------------------------------+
| 101   | badrpc                                  |
+-------+-----------------------------------------+
| 102   | Unknown error                           |
+-------+-----------------------------------------+
| 103   | Username or password error              |
+-------+-----------------------------------------+
| 104   | empty username or password              |
+-------+-----------------------------------------+
| 105   | user does not exist                     |
+-------+-----------------------------------------+
| 106   | admin can not be deleted                |
+-------+-----------------------------------------+
| 107   | missing request parameter               |
+-------+-----------------------------------------+
| 108   | request parameter type error            |
+-------+-----------------------------------------+
| 109   | request parameter is not a json         |
+-------+-----------------------------------------+
| 110   | plugin has been loaded                  |
+-------+-----------------------------------------+
| 111   | plugin has been unloaded                |
+-------+-----------------------------------------+
| 112   | User offline                            |
+-------+-----------------------------------------+
| 113   | User exists already                     |
+-------+-----------------------------------------+
| 114   | Wrong old password                      |
+-------+-----------------------------------------+

