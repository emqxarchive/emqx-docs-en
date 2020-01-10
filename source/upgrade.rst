
.. _upgrade:

=======
Upgrade
=======

.. _upgrade_3.1:

---------------
Upgrade to 3.1
---------------

.. NOTE:: The 3.1 version newly designed the project architecture, configuration method and plug-in management method. 2.x and 1.x version upgrades require reconfiguration deployment.

Upgrade steps:

1. Download and install emqx-3.1 to the new directory, for example /opt/emqx-3.1/.

2. Refer to the old version of etc/vm.args, etc/emqttd.config or etc/emq.conf to configure version 3.1 of etc/emqx.conf.

3. Reconfigure etc/plugins/{plugin}.conf.

4. Edit the data/loaded_plugins, and add the plugins loaded in old installation.

5. Stop the old emqttd, and start the 3.1 installation.

.. _upgrade_2.0.3:

-----------------
Upgrade to 2.0.3
-----------------

Upgrade steps:

1. Download and extract the 2.0.3 version to the new installation directory, for example /opt/emqttd-2.0.3/.

2. The old version of the 'etc/' configuration file, the 'data/' data file is overwritten to the new version of the directory.

3. Stop the old emqttd, and start the 2.0.3 installation.

.. _upgrade_2.0:

--------------
Upgrade to 2.0
--------------

.. NOTE:: Cannot upgrade 1.x releases to 2.0 smoothly.

Upgrade steps:

1. Download and install emqttd-2.0 to the new directory, for example::

    Old installation: /opt/emqttd-1.1.3/

    New installation: /opt/emqttd-2.0/

2. Configure the etc/emq.conf for the 2.0 installation.

3. Configure etc/plugins/{plugin}.conf for the 2.0 new installation if you loaded plugins.

4. Edit the data/loaded_plugins, and add the plugins loaded in old installation.

5. Stop the old emqttd, and start the 2.0 installation.

.. _upgrade_1.1.2:

----------------
Upgrade to 1.1.2
----------------

.. NOTE:: 1.0+ releases can be upgraded to 1.1.2 smoothly

Steps:

1. Download and install emqttd-1.1.2 to the new directory, for example::

    Old installation: /opt/emqttd_1_0_0/

    New installation: /opt/emqttd_1_1_2/

2. Copy the 'etc/' and 'data/' from the old installation::

    cp -R /opt/emqttd_1_0_0/etc/* /opt/emqttd_1_1_2/etc/

    cp -R /opt/emqttd_1_0_0/data/* /opt/emqttd_1_1_2/data/

3. Copy the plugins/{plugin}/etc/* from the old installation if you loaded plugins.

4. Stop the old emqttd, and start the new one.

===============
Migration guide
===============

The following provides a set of **guidelines** for migrating from **EMQ X 3.x** to the **EMQ X 4.0** version. Even though we tried to reduce a number of breaking changes, But in order to take into account the performance and simplify the way of use, we have made changes in several places.

**How long will it take to migrate a EMQ X 3.x project to EMQ X 4.0?**

EMQ X always guarantees the standardization and continuous update of the access protocol. During version migration, the client **does not need to make any adjustment**, which means they do not need to stop the device function and re-program the firmware of the device program. They only need to focus on changes to plug-ins, configuration items, command lines, and rest APIs.

The length of time required depends on the size of your project and the scope of the change. Small and medium-sized projects can be completed in one day.

----
Core
----

client_id changed to clientid
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

We have made a big change in the variable naming here. All client_id characters inside EMQ X are changed to clientid, including:

- URL of REST API, request / field name in corresponding data

- Naming conventions in source code

- Command Line Iterface

--------
REST API
--------

v3 chang to v4
>>>>>>>>>>>>>>

Change ``http(s)://host:8081/api/v3/`` to ``http(s)://host:8081/api/v4/``.

connection change to clients
>>>>>>>>>>>>>>>>>>>>>>>>>>>>

Change the concept of ``connections`` to ``clients``, involving the APIs related to nodes and clusters:

- Get the list of connections on the cluster: ``GET /connections`` -> ``/clients``

- Get cluster specific connection information: ``GET /connections/:clientid`` - > ``GET / connections/:clientid``

- Get the list of connections on a node: ``GET /nodes/:node/connections`` - > ``GET / nodes/:node/clients``

- Get node specific connection information: ``GET /nodes/:node/connections/:clientid`` - > ``GET /nodes/:node/clients/: clientid``

- The name of the client_id field in the request /response data changes to clientid

At the same time, the content returned by the API has been greatly changed, as shown in the 4.0 document.

Remove REST API for session
>>>>>>>>>>>>>>>>>>>>>>>>>>>

The concept of Channel was introduced in 4.0, combining session and client into one part. The following APIs have been **removed** in version 4.0:

- Get the session list on the cluster：``GET /sessions``

- Get the specified session information of the cluster：``GET /sessions/:clientid``

- Get node session list：``GET /nodes/:node/sessions``

- Get the client session information specified by the node：``GET /nodes/:node/sessions/:clientid``

To obtain session-related information after version 4.0, please use the client related API.

Remove plug-in configuration API
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

The plug-in configuration may contain sensitive information, and the plug-in configuration does not support persistence, which brings great confusion to users. Considering security issues and usability issues, we **removed** the plugin of acquisition and change of API.

- Get plug-in configuration information：``GET /nodes/:node/plugins/:plugin_name``

- Update plug-in configuration：``PUT /nodes/:node/plugins/:plugin_name``

We plan to solve the above problems in the **Enterprise version** through security specifications and local storage of configuration items, and re-provide plug-in hot-configuration related APIs. **The current enterprise version already supports hot-configuration operations for key configurations.**

---------
Dashboard
---------

Change connections to clients
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

The concept of **connections** in Dashboard is changed to **clients**, and the original connection information can be viewed on the current **clients** page.

Remove sessions page
>>>>>>>>>>>>>>>>>>>>

The **sessions** management page was removed from the Dashboard, and related information was integrated into the **clients** page.

Rule Engine
>>>>>>>>>>>

The SQL syntax of the rules engine has changed. The **Event** drop-down selection box is no longer provided in the Dashboard when the rules are created. For detailed changes to the SQL syntax, please refer to the **Rules Engine** section of this article.

-----------
Rule Engine
-----------

SQL syntax change
>>>>>>>>>>>>>>>>>

In version 4.0, the SQL syntax of the rule engine is easier to use. In version 3.x, all events **FROM** need to specify the event name after the clause. After 4.0, we introduce the concept of event topic. By default, the ``message.publish`` event no longer requires to specify event name:

.. code-block::

    ## 3.x
    ## Event name needs to be specified for processing
    SELECT * FROM "message.publish" WHERE topic =~ 't/#'

    ## 4.0 and later
    ## The message.publish event is processed by default, and mqtt topics are filtered directly after FROM
    ## The above SQL is equivalent to:
    SELECT * FROM 't/#'

    ## Other events are filtered by event topics
    SELECT * FROM "$evnents/message_acked" where topic =~ 't/#'
    SELECT * FROM "$evnents/client_connected"

The legacy SQL syntax conversion function is provided in dashboard to complete SQL upgrade and migration.

Change of event name
>>>>>>>>>>>>>>>>>>>>

In version 4.0, when the **subscription/unsubscribe** principal is changed to **session**, and **event** is converted to **event topic**, the following changes should be noted:

- **Client Subscribe** change to **Session Subscribed**：``client.subscribe`` -> ``$events/session_subscribed``
- **Client Unsubscribe** change to **Session Unsubscribed**：``client.unsubscribe`` -> ``$events/session_unsubscribed``
