
.. _install:

============
Installation
============

The *EMQ X* broker is cross-platform, which could be deployed on Linux, FreeBSD, Mac, Windows and even Raspberry Pi.

.. NOTE::

    Linux, FreeBSD Recommended.

.. _install_download:

-----------------
Download Packages
-----------------

Download binary packages from: http://emqtt.io/downloads

+-------------+-----------------------------------------------+
| Debian      | http://emqtt.com/downloads/latest/debian      |
+-------------+-----------------------------------------------+
| Ubuntu12.04 | http://emqtt.com/downloads/latest/ubuntu12_04 |
+-------------+-----------------------------------------------+
| Ubuntu14.04 | http://emqtt.com/downloads/latest/ubuntu14_04 |
+-------------+-----------------------------------------------+
| Ubuntu16.04 | http://emqtt.com/downloads/latest/ubuntu16_04 |
+-------------+-----------------------------------------------+
| CentOS7     | http://emqtt.com/downloads/latest/centos7     |
+-------------+-----------------------------------------------+
| Debian7     | http://emqtt.com/downloads/latest/debian7     |
+-------------+-----------------------------------------------+
| Debian8     | http://emqtt.com/downloads/latest/debian7     |
+-------------+-----------------------------------------------+
| FreeBSD     | http://emqtt.com/downloads/latest/freebsd     |
+-------------+-----------------------------------------------+
| Windows7    | http://emqtt.com/downloads/latest/windows7    |
+-------------+-----------------------------------------------+
| Windows10   | http://emqtt.com/downloads/latest/windows10   |
+-------------+-----------------------------------------------+
| Mac OS X    | http://emqtt.com/downloads/latest/macosx      |
+-------------+-----------------------------------------------+
| Docker      | http://emqtt.com/downloads/latest/docker      |
+-------------+-----------------------------------------------+

The package name consists of platform, version and release time.

For example: emqx-centos64-v3.0.zip

.. _install_on_linux:

-------------------
Installing on Linux
-------------------

Download CentOS Package from: http://emqtt.io/downloads/latest/centos7, and then unzip:

.. code-block:: bash

    unzip emqx-centos64-v3.0.zip

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
    Erlang MQTT Broker 2.0 is running now
    Eshell V6.4  (abort with ^G)
    (emqx@127.0.0.1)1>

CTRL+C to close the console and stop the broker.

Start the broker in daemon mode:

.. code-block:: bash

    ./bin/emqx start

Check the running status of the broker:

.. code-block:: bash

    $ ./bin/emqttd_ctl status
    Node 'emqx@127.0.0.1' is started
    emqx 2.0 is running

Or check the status by URL::

    http://localhost:8080/status

Stop the broker::

    ./bin/emqx stop

.. _install_via_rpm:

---------------
Install via RPM
---------------

Download the RPM packages:

+-------------+---------------------------------------------------+
| CentOS6.8   | http://emqtt.com/downloads/latest/centos6-rpm     |
+-------------+---------------------------------------------------+
| CentOS7     | http://emqtt.com/downloads/latest/centos7-rpm     |
+-------------+---------------------------------------------------+

Install the package:

.. code-block:: console

    rpm -ivh emqx-centos7-v2.1.2-1.el7.centos.x86_64.rpm

.. NOTE:: Erlang/OTP R19 depends on lksctp-tools library

.. code-block:: console

    yum install lksctp-tools

Configuration, Data and Log Files:

+---------------------------+-------------------------------------------+
| /etc/emqx/emq.conf        | Configuration file for the EMQ X Broker   |
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

Download the DEB packages:

+-------------+---------------------------------------------------+
| Ubuntu12.04 | http://emqtt.com/downloads/latest/ubuntu12_04-deb |
+-------------+---------------------------------------------------+
| Ubuntu14.04 | http://emqtt.com/downloads/latest/ubuntu14_04-deb |
+-------------+---------------------------------------------------+
| Ubuntu16.04 | http://emqtt.com/downloads/latest/ubuntu16_04-deb |
+-------------+---------------------------------------------------+
| Debian7     | http://emqtt.com/downloads/latest/debian7-deb     |
+-------------+---------------------------------------------------+
| Debian8     | http://emqtt.com/downloads/latest/debian7-deb     |
+-------------+---------------------------------------------------+

Install the package:

.. code-block:: console

    sudo dpkg -i emqx-ubuntu16.04_v2.0_amd64.deb

.. NOTE:: Erlang/OTP R19 depends on lksctp-tools library

.. code-block:: console

    apt-get install lksctp-tools

Configuration, Data and Log Files:

+------------------------------+-------------------------------------------+
| /etc/emqx/emq.conf           | Configuration file for the EMQ X Broker   |
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

.. _install_on_freebsd:

---------------------
Installing on FreeBSD
---------------------

Download FreeBSD Package from: http://emqtt.io/downloads/latest/freebsd

The installing process is same to Linux.

.. _install_on_mac:

----------------------
Installing on Mac OS X
----------------------

We could install the broker on Mac OS X to develop and debug MQTT applications.

Download Mac Package from: http://emqtt.io/downloads/latest/macosx

Configure log level in `etc/emq.conf`, all MQTT messages recevied/sent will be printed on console:

.. code-block:: bash

    ## Console log. Enum: off, file, console, both
    log.console = both

    ## Console log level. Enum: debug, info, notice, warning, error, critical, alert, emergency
    log.console.level = debug

    ## Console log file
    log.console.file = log/console.log

The install and boot process on Mac are same to Linux.

.. _install_on_windows:

---------------------
Installing on Windows
---------------------

Download Package from: http://emqtt.io/downloads/latest/windows.

Unzip the package to install folder. Open the command line window and 'cd' to the folder.

Start the broker in console mode::

    bin\emqx console

If the broker started successfully, a Erlang console window will popup.

Close the console window and stop the emqx broker. Prepare to register emqx as window service.

.. WARNING:: Cannot register EMQ X-2.0 as a windows service.

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

Download *EMQ X* 2.0 Docker Image:

    http://emqtt.com/downloads/latest/docker

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

    git clone https://github.com/emqtt/emq-relx.git

    cd emq-relx && make

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

Clone and build the `emq-relx`_ project:

.. code-block:: bash

    git clone -b windows https://github.com/emqtt/emqx-relx.git

    cd emqx-relx && make

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
| etc/emq.conf          | EMQ X Broker Config               |
+-----------------------+-----------------------------------+
| etc/plugins/\*.conf   | EMQ X Plugins' Config             |
+-----------------------+-----------------------------------+

Two important parameters in etc/emq.conf:

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

.. _emq_dashboard:       https://github.com/emqtt/emqttd_dashboard
.. _emq-relx:            https://github.com/emqtt/emq-relx
