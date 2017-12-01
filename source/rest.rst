
.. _rest_api:

========
REST API
========

The REST API allows you to query MQTT clients, sessions, subscriptions, and routes. You can also query and monitor the metrics and statistics of the broker.

--------
Base URL
--------

All REST APIs in the documentation have the following base URL::

    http(s)://host:8080/api/v2/

--------------------
Basic Authentication
--------------------

The HTTP requests to the REST API are protected with HTTP Basic authentication, For example:

.. code-block:: bash

    curl -v --basic -u <user>:<passwd> -k http://localhost:8080/api/v2/nodes/emq@127.0.0.1/clients


-----
Nodes
-----

List all Nodes in the Cluster
-----------------------------

Definition::

    GET api/v2/management/nodes

Response:

.. code-block:: json

    {
        "code": 0,
        "result":
        [
            {
                "name": "emq@127.0.0.1",
                "version": "2.1.1",
                "sysdescr": "EMQ",
                "uptime": "1 hours, 17 minutes, 1 seconds",
                "datetime": "2017-04-14 14:11:38",
                "otp_release": "R19/8.3",
                "node_status": "Running"
            }
        ]
    }

Retrieve a Node's Info
----------------------

Definition::

    GET api/v2/management/nodes/{node_name}

Example Request::

    GET api/v2/management/nodes/emq@127.0.0.1
 
Response:

.. code-block:: json

    {
        "code": 0,
        "result":
        {
            "version": "2.1.1",
            "sysdescr": "EMQ X",
            "uptime": "1 hours, 17 minutes, 18 seconds",
            "datetime": "2017-04-14 14:11:55",
            "otp_release": "R19/8.3",
            "node_status": "Running"
        }
    }

List all Nodes'statistics in the Cluster
----------------------------------------

Definition::

    GET api/v2/monitoring/nodes

Response:

.. code-block:: json

    {
        "code": 0,
        "result":
        [
            {
                "name": "emq@127.0.0.1",
                "otp_release": "R19/8.3",
                "memory_total": "69.19M",
                "memory_used": "49.28M",
                "process_available": 262144,
                "process_used": 303,
                "max_fds": 256,
                "clients": 1,
                "node_status": "Running",
                "load1": "1.93",
                "load5": "1.93",
                "load15": "1.89"
            }
        ]
    }

Retrieve a node's statistics
---------------------------

Definition::

    GET api/v2/monitoring/nodes/{node_name}

Example Request::

    GET api/v2/monitoring/nodes/emq@127.0.0.1

Response:

.. code-block:: json

    {
        "code": 0,
        "result":
        {
            "name": "emq@127.0.0.1",
            "otp_release": "R19/8.3",
            "memory_total": "69.19M",
            "memory_used": "49.24M",
            "process_available": 262144,
            "process_used": 303,
            "max_fds": 256,
            "clients": 1,
            "node_status": "Running",
            "load1": "2.21",
            "load5": "2.00",
            "load15": "1.92"
        }
    }

-------
Clients
-------

List all Clients on a Node
--------------------------

Definition::

    GET api/v2/nodes/{node_name}/clients

Request Parameter::

    curr_page={page_no}&page_size={page_size}

Example Request::

    GET api/v2/nodes/emq@127.0.0.1/clients

Response:

.. code-block:: json

    {
        "code": 0,
        "result":
        {
            "current_page": 1,
            "page_size": 20,
            "total_num": 1,
            "total_page": 1,
            "objects":
            [
                {
                    "client_id": "C_1492145414740",
                    "username": "undefined",
                    "ipaddress": "127.0.0.1",
                    "port": 49639,
                    "clean_sess": true,
                    "proto_ver": 4,
                    "keepalive": 60,
                    "connected_at": "2017-04-14 12:50:15"
                }
            ]
        }
    }

Retrieve a Client on a Node
--------------------------

Definition::

    GET api/v2/nodes/{node_name}/clients/{client_id}

Example Request::

    GET api/v2/nodes/emq@127.0.0.1/clients/C_1492145414740

Response:

.. code-block:: json


    {
        "code": 0,
        "result":
        {
            "objects":
            [
                {
                    "client_id": "C_1492145414740",
                    "username": "undefined",
                    "ipaddress": "127.0.0.1",
                    "port": 50953,
                    "clean_sess": true,
                    "proto_ver": 4,
                    "keepalive": 60,
                    "connected_at": "2017-04-14 13:35:15"
                }
            ]
        }
    }

Retrieve a Client in the Cluster
-------------------------------

Definition::

    GET api/v2/clients/{client_id}

Example Request::

    GET api/v2/clients/C_1492145414740

Response:

.. code-block:: json

    {
        "code": 0,
        "result":
        {
            "objects":
            [
                {
                    "client_id": "C_1492145414740",
                    "username": "undefined",
                    "ipaddress": "127.0.0.1",
                    "port": 50953,
                    "clean_sess": true,
                    "proto_ver": 4,
                    "keepalive": 60,
                    "connected_at": "2017-04-14 13:35:15"
                }
            ]
        }
    }

Disconnect a Specified Client in the Cluster 
--------------------------------------------

Definition::

    DELETE api/v2/clients/{clientid}

Example Request::

    DELETE api/v2/clients/C_1492145414740

Response:

.. code-block:: json

    {
        "code": 0,
        "result": []
    }

Clear the ACL of a Specified Client in the Cluster
--------------------------------------------------

Definition::

    PUT api/v2/clients/{clientid}/clean_acl_cache

Request Parameter:

.. code-block:: json

    {
        "topic": "test"
    }

Request Example::

    PUT api/v2/clients/C_1492145414740/clean_acl_cache

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

    GET api/v2/node/{node_name}/sessions?curr_page=1&page_size=20

Example Request::

    GET api/v2/nodes/emq@127.0.0.1/sessions

Response:

.. code-block:: json

    {
        "code": 0,
        "result":
        {
            "current_page": 1,
            "page_size": 20,
            "total_num": 1,
            "total_page": 1,
            "objects":
            [
                {
                    "client_id": "C_1492145414740",
                    "clean_sess": true,
                    "max_inflight": "undefined",
                    "inflight_queue": "undefined",
                    "message_queue": "undefined",
                    "message_dropped": "undefined",
                    "awaiting_rel": "undefined",
                    "awaiting_ack": "undefined",
                    "awaiting_comp": "undefined",
                    "created_at": "2017-04-14 13:35:15"
                }
            ]
        }
    }
    
Retrieve a Session on a Node
----------------------------

Definition::

    GET api/v2/nodes/{node_name}/sessions/{client_id}

Example Request::

    GET api/v2/nodes/emq@127.0.0.1/sessions/C_1492145414740

Response:

.. code-block:: json

    {
        "code": 0,
        "result":
        {
            "objects":
            [
                {
                    "client_id": "C_1492145414740",
                    "clean_sess": true,
                    "max_inflight": "undefined",
                    "inflight_queue": "undefined",
                    "message_queue": "undefined",
                    "message_dropped": "undefined",
                    "awaiting_rel": "undefined",
                    "awaiting_ack": "undefined",
                    "awaiting_comp": "undefined",
                    "created_at": "2017-04-14 13:35:15"
                }
            ]
        }
    }

Retrieve a Session in the Cluster
--------------------------------

Definition::

    GET api/v2/sessions/{client_id}

Example Request::

    GET api/v2/sessions/C_1492145414740

Response:

.. code-block:: json

    {
        "code": 0,
        "result":
        {
            "objects":
            [
                {
                    "client_id": "C_1492145414740",
                    "clean_sess": true,
                    "max_inflight": "undefined",
                    "inflight_queue": "undefined",
                    "message_queue": "undefined",
                    "message_dropped": "undefined",
                    "awaiting_rel": "undefined",
                    "awaiting_ack": "undefined",
                    "awaiting_comp": "undefined",
                    "created_at": "2017-04-14 13:35:15"
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

    GET api/v2/nodes/{node_name}/subscriptions
    
Request parameters::

    curr_page={page_no}&page_size={page_size}

Example Request::

    GET api/v2/nodes/emq@127.0.0.1/subscriptions

Response:

.. code-block:: json

    {
        "code": 0,
        "result":
        {
            "current_page": 1,
            "page_size": 20,
            "total_num": 1,
            "total_page": 1,
            "objects":
            [
                {
                    "client_id": "C_1492145414740",
                    "topic": "$client/C_1492145414740",
                    "qos": 1
                }
            ]
        }
    }
    
List Subscriptions of a Client
------------------------------

Definition::

    GET api/v2/subscriptions/{cliet_id}

Example Request::

    GET api/v2/subscriptions/C_1492145414740

Response:

.. code-block:: json

    {
        "code": 0,
        "result":
        {
            "objects":
            [
                {
                    "client_id": "C_1492145414740",
                    "topic": "$client/C_1492145414740",
                    "qos": 1
                }
            ]
        }
    }

Create a Subscription
----------------------

Definition::

    POST api/v2/mqtt/subscribe

Request parameters:

.. code-block:: json

    {
        "topic": "test",
        "qos": 1,
        "client_id": "C_1492145414740"
    }

Response:

.. code-block:: json

    {
        "code": 0,
        "result": []
    }

------
Routes
------

List all Routes in the Cluster
-------------------------------

Definition::

    GET api/v2/routes

Request parameters::

    curr_page={page_no}&page_size={page_size}

Response:

.. code-block:: json

    {
        "code": 0,
        "result":
        {
            "current_page": 1,
            "page_size": 20,
            "total_num": 1,
            "total_page": 1,
            "objects":
            [
                {
                    "topic": "$client/C_1492145414740",
                    "node": "emq@127.0.0.1"
                }
            ]
        }
    }

Retrieve a Route in the Cluster
-------------------------------

Definition::

    GET api/v2/routes/{topic}

Example Request::

    GET api/v2/routes/topic

Response:

.. code-block:: json

    {
        "code": 0,
        "result":
        {
            "objects":
            [
                {
                    "topic": "topic",
                    "node": "emq@127.0.0.1"
                }
            ]
        }
    }

-------
Plugins
-------

List all Plugins of a Node
--------------------------

Definition::

    GET /api/v2/nodes/{node_name}/plugins/

Response:

.. code-block:: json

    {
        "code": 0,
        "result": [
            {
                "name": "emq_auth_clientid",
                "version": "2.3",
                "description": "Authentication with ClientId/Password",
                "active": false
            },
            {
                "name": "emq_auth_http",
                "version": "2.3",
                "description": "Authentication/ACL with HTTP API",
                "active": false
            },
            {
                "name": "emq_auth_jwt",
                "version": "2.3",
                "description": "Authentication with jwt",
                "active": false
            }, 
            {
                "name": "emq_auth_ldap",
                "version": "2.3",
                "description": "Authentication/ACL with LDAP",
                "active": false
            },
            {
                "name": "emq_auth_mongo",
                "version": "2.3",
                "description": "Authentication/ACL with MongoDB",
                "active": false
            },
            {
                "name": "emq_auth_mysql",
                "version": "2.3",
                "description": "Authentication/ACL with MySQL",
                "active": false
            },
            {
                "name": "emq_auth_pgsql",
                "version": "2.3",
                "description": "Authentication/ACL with PostgreSQL",
                "active": false
            },
            {
                "name": "emq_auth_redis",
                "version": "2.3",
                "description": "Authentication/ACL with Redis",
                "active": false
            },
            {
                "name": "emq_auth_username",
                "version": "2.3",
                "description": "Authentication with Username/Password",
                "active": false
            },
            {
                "name": "emq_coap",
                "version": "2.3",
                "description": "CoAP Gateway",
                "active": false
            },
            {
                "name": "emq_dashboard",
                "version": "2.3",
                "description": "EMQ Web Dashboard",
                "active": true
            },
            {
                "name": "emq_lua_hook",
                "version": "2.3",
                "description": "EMQ hooks in lua",
                "active": false
            },
            {
                "name": "emq_lwm2m",
                "version": "0.1",
                "description": "LWM2M Gateway",
                "active": false
            },
            {
                "name": "emq_modules",
                "version": "2.3",
                "description": "EMQ Modules",
                "active": true
            },
            {
                "name": "emq_plugin_template",
                "version": "2.3",
                "description": "EMQ Plugin Template",
                "active": false
            },
            {
                "name": "emq_recon",
                "version": "2.3",
                "description": "Recon Plugin",
                "active": true
            },
            {
                "name": "emq_reloader",
                "version": "2.3",
                "description": "Reloader Plugin",
                "active": false
            },
            {
                "name": "emq_retainer",
                "version": "2.3",
                "description": "EMQ Retainer",
                "active": true
            },
            {
                "name": "emq_sn",
                "version": "2.3",
                "description": "MQTT-SN Gateway",
                "active": false
            },
            {
                "name": "emq_stomp",
                "version": "2.3",
                "description": "Stomp Protocol Plugin",
                "active": false
            },
            {
                "name": "emq_web_hook",
                "version": "2.3",
                "description": "EMQ Webhook Plugin",
                "active": false
            }
        ]
    }

Start/Stop a Plugin
-------------------

Definition::

    PUT /api/v2/nodes/plugins/{name}

Request parameters:

.. code-block:: json 

    {
        "active": true/false,
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

    GET api/v2/monitoring/listeners

Response:

.. code-block:: json

    {
        "code": 0,
        "result": {
            "emq@127.0.0.1": [
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

    GET api/v2/monitoring/listeners/{node_name}

Example Request::

    GET api/v2/monitoring/listeners/emq@127.0.0.1
    
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

--------
Messages
--------

Publish MQTT Message
--------------------

Definition::

    POST api/v2/mqtt/publish

Request parameters:

.. code-block:: json

    {
        "topic": "test",
        "payload": "hello",
        "qos": 1,
        "retain": false,
        "client_id": "C_1492145414740"
    }
    
Response:

.. code-block:: json

    {
        "code": 0,
        "result": []
    }

-------
Metrics
-------

Get Metrics of all Nodes
-------------------------

Definition::

    GET api/v2/monitoring/metrics/

Response:

.. code-block:: json

    {
        "code": 0,
        "result": {
            "emq@127.0.0.1":
            {
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
    }

Get Metrics of a Node
---------------------

Definition::

    GET api/v2/monitoring/metrics/{node_name}

Example Request::

    GET api/v2/monitoring/metrics/emq@127.0.0.1

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

----------
Statistics
----------

Get Statistics of all Nodes
----------------------------

Definition::

    GET api/v2/monitoring/stats

Example Request::

    GET api/v2/monitoring/stats

Response:

.. code-block:: json

    {
        "code": 0,
        "result": {
            "emq@127.0.0.1":
            {
                "clients/count":0,
                "clients/max":0,
                "retained/count":0,
                "retained/max":0,
                "routes/count":0,
                "routes/max":0,
                "sessions/count":0,
                "sessions/max":0,
                "subscribers/count":0,
                "subscribers/max":0,
                "subscriptions/count":0,
                "subscriptions/max":0,
                "topics/count":0,
                "topics/max":0
            }
        }
    }

Get Statistics of a Node
------------------------

Definition::

    GET api/v2/monitoring/stats/{node_name}

Example Request::

    GET api/v2/monitoring/stats/emq@127.0.0.1

Response:

.. code-block:: json

    {
        "code": 0,
        "result": {
            "clients/count":0,
            "clients/max":0,
            "retained/count":0,
            "retained/max":0,
            "routes/count":0,
            "routes/max":0,
            "sessions/count":0,
            "sessions/max":0,
            "subscribers/count":0,
            "subscribers/max":0,
            "subscriptions/count":0,
            "subscriptions/max":0,
            "topics/count":0,
            "topics/max":0
        }
    }

---------------
User Management
---------------

Retrieve Admin User List
------------------------

Definition::

    GET api/v2/users

Request Example::

    GET api/v2/users

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

    POST api/v2/users

Request Parameter::

    {
        "username": "test_user",
        "password": "password",
        "tags": "user"
    }

Request Example::

    POST api/v2/users

Response:

.. code-block:: json

    {
        "code": 0,
        "result": []
    }

Modify Admin User Information
-----------------------------

Definition::

    PUT api/v2/users/{username}

Request Parameter::

    {
        "tags": "admin"
    }

Request Example::

    PUT api/v2/users/test_user

Response:

.. code-block:: json

    {
        "code": 0,
        "result": []
    }

Delete Admin User
-----------------

Definition::

    DELETE api/v2/users/{username}

Request Parameter::


Request Example::

    DELETE api/v2/users/test_user

Response:

.. code-block:: json

    {
        "code": 0,
        "result": []
    }

Authenticate Admin User
-----------------------

Definition::

    POST api/v2/auth

Request Parameter::

    {
        "username": "test_user",
        "password": "password"
    }

Request Example::

    POST api/v2/auth

Response:

.. code-block:: json

    {
        "code": 0,
        "result": []
    }

Modify Admin User Password
--------------------------

Definition::

    PUT api/v2/change_pwd/{username}

Request Parameter::

    {
        "password": "newpassword",
        "oldpassword": "password"
    }

Request Example::

    PUT api/v2/change_pwd/test_user

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

