# Installation

The EMQ X Edge broker V3.1 supports CentOS, Alpine, Raspbian, Debian, Ubuntu and Docker.

## Download Packages

Download binary packages from: [ https://www.emqx.io/downloads/edge ](https://www.emqx.io/downloads/edge)

The following OS types are supported:

- CentOS6.8
- CentOS7  
- Debian7  
- Debian8  
- Debian9  
- Alpine3.8  
- Raspbian8  
- Raspbian9  
- Ubuntu12.04
- Ubuntu14.04
- Ubuntu16.04
- Ubuntu18.04
- Docker

The package name consists of platform, version and release time.

For example: ` emqx-edge-centos7-v3.1-beta1.zip `

## Install via zip

Select and download zip package from [ https://www.emqx.io/downloads/edge ](https://www.emqx.io/downloads/edge) , and then unzip:

    unzip emqx-edge-centos7-v3.1-beta1.zip

Start the Edge broker in console mode:

    cd emqx && ./bin/emqx console

If the broker is started successfully, console will print:

    starting emqx on node 'emqx@127.0.0.1'
    start 'http:management' listener on 8080 successfully.
    Start 'mqtt:tcp' listener on 127.0.0.1:11883 successfully.
    Start 'mqtt:tcp' listener on 0.0.0.0:1883 successfully.
    Start 'mqtt:ws' listener on 0.0.0.0:8083 successfully.
    Start 'mqtt:ssl' listener on 0.0.0.0:8883 successfully.
    Start 'mqtt:wss' listener on 0.0.0.0:8084 successfully.
    EMQ X Broker 3.1 is running now!
    Eshell V10.2.1  (abort with ^G)

CTRL+C to close the console and stop the broker.

Start the broker in daemon mode:

    ./bin/emqx start

Check the running status of the broker:

    $ ./bin/emqx_ctl status
    Node 'emqx@127.0.0.1' is started
    emqx 3.1 is running

Or check the status by URL:

    http://localhost:8080/status

Stop the broker:

    ./bin/emqx stop

## Install via RPM

Select Linux group from [ https://www.emqx.io/downloads/edge ](https://www.emqx.io/downloads/edge) , and download the RPM packages.

- CentOS6.8
- CentOS7

Install the package:

    rpm -ivh emqx-edge-centos7-v3.1.x86_64.rpm

::: tip Tip
Erlang/OTP R19 depends on lksctp-tools library
:::

    yum install lksctp-tools

Configuration, Data and Log Files:

| /etc/emqx/emqx.conf       | Configuration file for the Edge Broker   |
| ------------------------- | ---------------------------------------- |
| /etc/emqx/plugins/\*.conf | Configuration files for the Edge Plugins |
| /var/lib/emqx/            | Data files                               |
| /var/log/emqx             | Log files                                |

Start/Stop the broker:

    systemctl start|stop|restart emqx.service

## Install via DEB

Select Linux group from [ https://www.emqx.io/downloads/edge ](https://www.emqx.io/downloads/edge) , and download the DEB packages.

- Raspbian8  
- Raspbian9  
- Debian7  
- Debian8  
- Debian9  
- Ubuntu12.04
- Ubuntu14.04
- Ubuntu16.04
- Ubuntu18.04

Install the package:

    sudo dpkg -i emqx-edge-raspbian8-v3.1_beta1_armhf.deb

::: tip Tip
Erlang/OTP R19 depends on lksctp-tools library
:::

    apt-get install lksctp-tools

Configuration, Data and Log Files:

| /etc/emqx/emqx.conf       | Configuration file for the Edge Broker   |
| ------------------------- | ---------------------------------------- |
| /etc/emqx/plugins/\*.conf | Configuration files for the Edge Plugins |
| /var/lib/emqx/            | Data files                               |
| /var/log/emqx             | Log files                                |

Start/Stop the broker:

    service emqx start|stop|restart

## Install via Docker Image

Select Docker group from [ https://www.emqx.io/downloads/edge ](https://www.emqx.io/downloads/edge) , and download EMQ X Edge 3.1 Beta1 Docker Image.

unzip emqx-edge-docker image:

    unzip emqx-edge-docker-v3.1-beta1.zip

Load Docker Image:

    docker load \< emqx-edge-docker-v3.1-beta1

Run the Container:

    docker run -tid --name emq31 -p 1883:1883 -p 8083:8083 -p 8883:8883 -p 8084:8084 -p 8080:8080 -p 18083:18083 emqx-edge-docker-v3.1-beta1

Stop the broker:

    docker stop emq31

Start the broker:

    docker start emq31

Enter the running container:

    docker exec -it emq31 /bin/sh

## Installing From Source

The EMQ X Edge broker 3.1 requires Erlang/OTP R21+ and git client to build:

Install Erlang: [ http://www.erlang.org/ ](http://www.erlang.org/)

Install Git Client: [ http://www.git-scm.com/ ](http://www.git-scm.com/)

Could use apt-get on Ubuntu, yum on CentOS/RedHat and brew on Mac to install Erlang and Git.

When all dependencies are ready, clone the emqx project from github.com and build:

    git clone https://github.com/emqx/emqx-rel.git

    cd emqx-rel && make

    cd _rel/emqx && ./bin/emqx console

The binary package output in folder:

    _rel/emqx

## TCP Ports Used

| 1883 | MQTT Port                |
| ---- | ------------------------ |
| 8883 | MQTT/SSL Port            |
| 8083 | MQTT/WebSocket/SSL Port  |
| 8084 | MQTT/WebSocket Port      |
| 8080 | HTTP Management API Port |

The TCP ports used can be configured in etc/emqx.config:

    ## TCP Listener: 1883, 127.0.0.1:1883, ::1:1883
    listener.tcp.external = 0.0.0.0:1883

    ## SSL Listener: 8883, 127.0.0.1:8883, ::1:8883
    listener.ssl.external = 8883

    ## External MQTT/WebSocket Listener
    listener.ws.external = 8083

## Quick Setup

Two main configuration files of the EMQ X Edge broker:

| etc/emqx.conf       | EMQ X Edge Broker Config   |
| ------------------- | -------------------------- |
| etc/plugins/\*.conf | EMQ X Edge Plugins' Config |

Two important parameters in etc/emqx.conf:

| node.process_limit | Max number of Erlang proccesses. A MQTT client consumes two proccesses. The value should be larger than max_clients \* 2 |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------ |
| node.max_ports     | Max number of Erlang Ports. A MQTT client consumes one port. The value should be larger than max_clients.                |

::: tip Tip
node.process_limit > maximum number of allowed concurrent clients \* 2 node.max_ports > maximum number of allowed concurrent clients
:::

The maximum number of allowed MQTT clients:

    listener.tcp.external = 0.0.0.0:1883

    listener.tcp.external.acceptors = 8

    listener.tcp.external.max_clients = 1024

## /etc/init.d/emqx

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

chkconfig:

    chmod +x /etc/init.d/emqx
    chkconfig --add emqx
    chkconfig --list

boot test:

    service emqx start

::: tip Tip
erlexec: HOME must be set uncomment '# export HOME=/root' if "HOME must be set" error.
:::
