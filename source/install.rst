
.. _install:

============
Installation
============

The *EMQ X* broker is cross-platform, which could be deployed on Linux, FreeBSD, Mac, Windows and even Raspberry Pi.

.. NOTE::

    Linux and FreeBSD Recommended.

.. _install_download:

-----------------
Download Packages
-----------------

Prebuilt releases and packages are available from https://www.emqx.io/downloads/broker

The following environments are supported. For each supported OS, a precompiled zipped bundle is provided.
Packages in rpm and deb formats are also available for RedHat and Debian family OS.

+-------------+
| CentOS6.8   |
+-------------+
| CentOS7     |
+-------------+
| Debian7     |
+-------------+
| Debian8     |
+-------------+
| Debian9     |
+-------------+
| Ubuntu12.04 |
+-------------+
| Ubuntu14.04 |
+-------------+
| Ubuntu16.04 |
+-------------+
| Ubuntu18.04 |
+-------------+
| Mac OS X    |
+-------------+
| Windows7    |
+-------------+
| Windows10   |
+-------------+
| Docker      |
+-------------+

The package name indicates flavor, platform, version and cpu architecture where applicable.

For example: emqx-centos6.8-v3.0.1.zip is a zipped release of EMQX version v3.0 suitable for running on CentOS 6.8.

.. _install_on_linux:

-------------------------
Installing zipped release
-------------------------

Select appropriate release zip from https://www.emqx.io/downloads/broker, download, and then unzip:

The instructions are identical for all Linuxes and Mac OSX. we'll use CentOS 7 in the examples below.

.. code-block:: bash

    unzip emqx-centos7-v3.0.zip

Start the broker in console mode:

.. code-block:: bash

    cd emqx && ./bin/emqx console

If the broker is started successfully, console will print:

.. code-block:: bash

    starting emqx on node 'emqx@127.0.0.1'
    emqx ctl is starting...[done]
    emqx trace is starting...[done]
    emqx pubsub is starting...[done]
    emqx stats is starting...[done]
    emqx metrics is starting...[done]
    emqx retainer is starting...[done]
    emqx pooler is starting...[done]
    emqx client manager is starting...[done]
    emqx session manager is starting...[done]
    emqx session supervisor is starting...[done]
    emqx broker is starting...[done]
    emqx alarm is starting...[done]
    emqx mod supervisor is starting...[done]
    emqx bridge supervisor is starting...[done]
    emqx access control is starting...[done]
    emqx system monitor is starting...[done]
    http listen on 0.0.0.0:18083 with 4 acceptors.
    mqtt listen on 0.0.0.0:1883 with 16 acceptors.
    mqtts listen on 0.0.0.0:8883 with 4 acceptors.
    http listen on 0.0.0.0:8083 with 4 acceptors.
    Erlang MQTT Broker 3.0 is running now
    Eshell V6.4  (abort with ^G)
    (emqx@127.0.0.1)1>

CTRL+C to close the console and stop the broker.

Start the broker in daemon mode:

.. code-block:: bash

    ./bin/emqx start

Check the running status of the broker:

.. code-block:: bash

    $ ./bin/emqx_ctl status
    Node 'emqx@127.0.0.1' is started
    emqx 3.0 is running

Or check the status by URL::

    http://localhost:8080/status

Stop the broker::

    ./bin/emqx stop

Configuration, Data and Log Files for zipped releases are alongside the broker.
The following paths are relative to the folder ``emqx`` extracted above.

+---------------------------+-------------------------------------------+
| etc/emqx.conf             | Configuration file for the EMQ X Broker   |
+---------------------------+-------------------------------------------+
| etc/plugins/\*.conf       | Configuration files for the EMQ X Plugins |
+---------------------------+-------------------------------------------+
| data                      | Data files                                |
+---------------------------+-------------------------------------------+
| log                       | Log files                                 |
+---------------------------+-------------------------------------------+

.. _install_via_rpm:

---------------
Install via RPM
---------------

RPM Packages are available for the following Operating Systems:

+-------------+
| CentOS6.8   |
+-------------+
| CentOS7     |
+-------------+

Select and download the appropriate package from https://www.emqx.io/downloads/broker.

Install the package:

.. code-block:: console

    rpm -ivh emqx-centos7-v3.0.1.x86_64.rpm

.. NOTE:: Erlang/OTP R19 depends on lksctp-tools library

.. code-block:: console

    yum install lksctp-tools

Configuration, Data and Log Files:

+---------------------------+-------------------------------------------+
| /etc/emqx/emqx.conf       | Configuration file for the EMQ X Broker   |
+---------------------------+-------------------------------------------+
| /etc/emqx/plugins/\*.conf | Configuration files for the EMQ X Plugins |
+---------------------------+-------------------------------------------+
| /var/lib/emqx/            | Data files                                |
+---------------------------+-------------------------------------------+
| /var/log/emqx             | Log files                                 |
+---------------------------+-------------------------------------------+

Start/Stop the broker:

.. code-block:: console

    systemctl start|stop|restart emqx.service

.. _install_via_deb:

---------------
Install via DEB
---------------

RPM Packages are available for the following Operating Systems:

+-------------+
| Ubuntu12.04 |
+-------------+
| Ubuntu14.04 |
+-------------+
| Ubuntu16.04 |
+-------------+
| Ubuntu18.04 |
+-------------+
| Debian7     |
+-------------+
| Debian8     |
+-------------+
| Debian9     |
+-------------+

Select and download the appropriate package from https://www.emqx.io/downloads/broker.

Install the package:

.. code-block:: console

    sudo dpkg -i emqx-ubuntu18.04-v3.0.1_amd64.deb

.. NOTE:: Erlang/OTP R19 depends on lksctp-tools library

.. code-block:: console

    apt-get install lksctp-tools

Configuration, Data and Log Files:

+------------------------------+-------------------------------------------+
| /etc/emqx/emqx.conf          | Configuration file for the EMQ X Broker   |
+------------------------------+-------------------------------------------+
| /etc/emqx/plugins/\*.conf    | Configuration files for the EMQ X Plugins |
+------------------------------+-------------------------------------------+
| /var/lib/emqx/               | Data files                                |
+------------------------------+-------------------------------------------+
| /var/log/emqx                | Log files                                 |
+------------------------------+-------------------------------------------+

Start/Stop the broker:

.. code-block:: console

    service emqx start|stop|restart

.. _install_on_windows:

---------------------
Installing on Windows
---------------------

Select Windows group from https://www.emqx.io/downloads/broker, and download the package.

Unzip the package to install folder. Open the command line window and 'cd' to the folder.

Start the broker in console mode::

    bin\emqx console

If the broker started successfully, a Erlang console window will popup.

Close the console window and stop the emqx broker. Prepare to register emqx as window service.

.. WARNING:: EMQ X-3.0 cannot be registered as a windows service.

Install emqx serivce::

    bin\emqx install

Start emqx serivce::

    bin\emqx start

Stop emqx serivce::

    bin\emqx stop

Uninstall emqx service::

    bin\emqx uninstall

.. _install_via_docker_image:

------------------------
Install via Docker Image
------------------------

Select Docker group from https://www.emqx.io/downloads/broker, and download *EMQ X* 3.0 Docker Image.

unzip emqx-docker image::

    unzip emqx-docker-v3.0.zip

Load Docker Image::

    docker load < emqx-docker-v3.0

Run the Container::

    docker run -tid --name emq30 -p 1883:1883 -p 8083:8083 -p 8883:8883 -p 8084:8084 -p 8080:8080 -p 18083:18083 emqx-docker-v3.0

Stop the broker::

    docker stop emq30

Start the broker::

    docker start emq30

Enter the running container::

    docker exec -it emq30 /bin/sh

.. _build_from_source:

----------------------
Installing From Source
----------------------

The *EMQ X* broker 3.0 requires Erlang/OTP R21+ and git client to build:

Install Erlang: http://www.erlang.org/

Install Git Client: http://www.git-scm.com/

Could use apt-get on Ubuntu, yum on CentOS/RedHat and brew on Mac to install Erlang and Git.

When all dependencies are ready, clone the emqx project from github.com and build:

.. code-block:: bash

    git clone https://github.com/emqx/emqx-rel.git

    cd emqx-rel && make

    cd _rel/emqx && ./bin/emqx console

The binary package output in folder::

    _rel/emqx

----------------
Build on Windows
----------------

Install Erlang: http://www.erlang.org/

Install MSYS2: http://www.msys2.org/

Use pacman of MSYS2 to install git and make:

.. code-block:: bash

    pacman -S git make

Clone and build the `emqx-rel`_ project:

.. code-block:: bash

    git clone -b windows https://github.com/emqx/emqx-rel.git

    cd emqx-rel && make

Start the EMQ X in console mode:

.. code-block:: bash

    cd _rel/emqx && ./bin/emqx console

.. _tcp_ports:

--------------
TCP Ports Used
--------------

+-----------+-----------------------------------+
| 1883      | MQTT Port                         |
+-----------+-----------------------------------+
| 8883      | MQTT/SSL Port                     |
+-----------+-----------------------------------+
| 8083      | MQTT/WebSocket Port               |
+-----------+-----------------------------------+
| 8084      | MQTT/WebSocket/SSL Port           |
+-----------+-----------------------------------+
| 8080      | HTTP Management API Port          |
+-----------+-----------------------------------+
| 18083     | Web Dashboard Port                |
+-----------+-----------------------------------+

The TCP ports used can be configured in etc/emqx.config:

.. code-block:: properties

    ## TCP Listener: 1883, 127.0.0.1:1883, ::1:1883
    listener.tcp.external = 0.0.0.0:1883

    ## SSL Listener: 8883, 127.0.0.1:8883, ::1:8883
    listener.ssl.external = 8883

    ## External MQTT/WebSocket Listener
    listener.ws.external = 8083

    ## HTTP Management API Listener
    listener.api.mgmt = 127.0.0.1:8080

The 18083 port is used by Web Dashboard of the broker. Default login: admin, Password: public

.. _quick_setup:

-----------
Quick Setup
-----------

Two main configuration files of the *EMQ X* broker:

+-----------------------+-----------------------------------+
| etc/emqx.conf         | EMQ X Broker Config               |
+-----------------------+-----------------------------------+
| etc/plugins/\*.conf   | EMQ X Plugins' Config             |
+-----------------------+-----------------------------------+

Two important parameters in etc/emqx.conf:

+--------------------+-------------------------------------------------------------------------+
| node.process_limit | Max number of Erlang proccesses. A MQTT client consumes two proccesses. |
|                    | The value should be larger than max_clients * 2                         |
+--------------------+-------------------------------------------------------------------------+
| node.max_ports     | Max number of Erlang Ports. A MQTT client consumes one port.            |
|                    | The value should be larger than max_clients.                            |
+--------------------+-------------------------------------------------------------------------+

.. NOTE::

    node.process_limit > maximum number of allowed concurrent clients * 2
    node.max_ports > maximum number of allowed concurrent clients

The maximum number of allowed MQTT clients:

.. code-block:: properties

    listener.tcp.external = 0.0.0.0:1883

    listener.tcp.external.acceptors = 8

    listener.tcp.external.max_clients = 1024


.. _suggested_development_config:

----------------------------
Suggested development config
----------------------------
During development, it might be convenient to print all MQTT messages recevied/sent for debugging purposes.

This can be achieved by setting log level to debug in `etc/emqx.conf`, 

.. code-block:: bash

    ## Console log. Enum: off, file, console, both
    log.console = both

    ## Console log level. Enum: debug, info, notice, warning, error, critical, alert, emergency
    log.console.level = debug

    ## Console log file
    log.console.file = log/console.log


.. _init_d_emqttd:

-------------------
/etc/init.d/emqx
-------------------

.. code-block:: bash

    #!/bin/sh
    #
    # emqx       Startup script for emqx.
    #
    # chkconfig: 2345 90 10
    # description: emqx is mqtt broker.

    # source function library
    . /etc/rc.d/init.d/functions

    # export HOME=/root

    start() {
        echo "starting emqx..."
        cd /opt/emqx && ./bin/emqx start
    }

    stop() {
        echo "stopping emqx..."
        cd /opt/emqx && ./bin/emqx stop
    }

    restart() {
        stop
        start
    }

    case "$1" in
        start)
            start
            ;;
        stop)
            stop
            ;;
        restart)
            restart
            ;;
        *)
            echo $"Usage: $0 {start|stop}"
            RETVAL=2
    esac


chkconfig::

    chmod +x /etc/init.d/emqx
    chkconfig --add emqx
    chkconfig --list

boot test::

    service emqx start

.. NOTE::

    ## erlexec: HOME must be set
    uncomment '# export HOME=/root' if "HOME must be set" error.

.. _emq_dashboard:       https://github.com/emqx/emqx-dashboard
.. _emqx-rel:            https://github.com/emqx/emqx-rel
