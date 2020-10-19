# Module management

The EMQ X release package provides a wealth of functional modules, including authentication and authorization, protocol access, message delivery, multi-language extension, operation and maintenance monitoring, and internal modules.
The module management page can start and close the module, as well as the configuration and data management of the module.

## Module list

The modules currently provided by the EMQ X release package include:

- Authentication
  - Builtin ACL file
  - MySQL Authentication/ACL
  - PostgreSQL Authentication/ACL
  - Redis Authentication/ACL
  - HTTP Authentication/ACL
  - Builtin database Authentication/ACL
  - MongoDB Authentication/ACL
  - LDAP Authentication/ACL
  - JWT Authentication
- Protocol access
  - LwM2M protocol gateway
  - MQTT-SN protocol gateway
  - TCP protocol gateway
  - JT/T808 Protocol Gateway
  - CoAP protocol gateway
  - Stomp protocol gateway
- Message delivery
  - Kafka consumer group
  - Pulsar Consumer Group
  - MQTT subscribers
- Multilingual extension
  - Protocol access
  - Hook
- DevOpts
  - Recon
  - Prometheus Agent
- Internal module
  - Topic metrics
  - MQTT enhanced certification
  - MQTT online and offline notification
  - MQTT broker subscription
  - MQTT topic rewrite
  - MQTT retainr messages
  - MQTT delayed publish


## Start and stop module

There are currently two ways to start the module:

1. Load by default
2. Use Dashboard to start and stop the module


**Enable default loading**

If you need to start a certain module by default when EMQ X starts, you can directly add the name of the module that needs to be started in `data/loaded_modules`.

For example, the modules currently automatically loaded by EMQ X are:

```json
[
    {
    "name": "internal_acl",
    "enable": true,
    "configs": {"acl_rule_file": "{{ acl_file }}"}
    },
    {
        "name": "presence",
        "enable": true,
        "configs": {"qos": 0}
    },
    {
        "name": "recon",
        "enable": true,
        "configs": {}
    },
    {
        "name": "retainer",
        "enable": true,
        "configs": {
            "expiry_interval": 0,
            "max_payload_size": "1MB",
            "max_retained_messages": 0,
            "storage_type": "ram"
        }
    }
]
```

**Use Dashboard to start and stop the module**

If the module of Dashbord is enabled, you can directly start and stop the module by visiting the module management page in `http://localhost:18083/modules`.