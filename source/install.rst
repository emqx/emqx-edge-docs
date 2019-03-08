
.. _install:

=======================
程序安装 (Installation)
=======================

*EMQ X Edge* 消息服务器3.1版本支持 CentOS、Alpine、Raspbian和Docker镜像安装。

.. _install_via_zip:


----------------
Linux 通用包安装
----------------

下载地址: https://www.emqx.io/downloads/edge

+-------------+-----------------------------------------------+
| CentOS7     | emqx-edge-centos7-v3.1-beta1.zip              |
+-------------+-----------------------------------------------+
| Alpine3.8   | emqx-edge-alpine3.8-amd64-v3.1-beta1.zip      |
+-------------+-----------------------------------------------+
| Alpine3.8   | emqx-edge-alpine3.8-arm64v8-v3.1-beta1.zip    |
+-------------+-----------------------------------------------+
| Raspbian8   | emqx-edge-raspbian8-v3.1-beta1.zip            |
+-------------+-----------------------------------------------+
| Raspbian9  | emqx-edge-raspbian9-v3.1-beta1.zip             |
+-------------+-----------------------------------------------+

安装包命名由平台、版本组成，例如: emqx-edge-centos7-v3.1-beta1.zip

以CentOS 平台为例，下载安装过程:

.. code-block:: bash

    unzip emqx-edge-centos7-v3.1-beta1.zip

控制台调试模式启动，检查 *EMQ X Edge* 是否可正常启动:

.. code-block:: bash

    cd emqx && ./bin/emqx console

*EMQ X Edge* 消息服务器如启动正常，控制台输出:

.. code-block:: bash

    starting emqx on node 'emqx@127.0.0.1'
    start 'http:management' listener on 8080 successfully.
    Start 'mqtt:tcp' listener on 127.0.0.1:11883 successfully.
    Start 'mqtt:tcp' listener on 0.0.0.0:1883 successfully.
    Start 'mqtt:ws' listener on 0.0.0.0:8083 successfully.
    Start 'mqtt:ssl' listener on 0.0.0.0:8883 successfully.
    Start 'mqtt:wss' listener on 0.0.0.0:8084 successfully.
    EMQ X Broker 3.1 is running now!
    Eshell V10.2.1  (abort with ^G)

CTRL+C 关闭控制台。

守护进程模式启动:

.. code-block:: bash

    ./bin/emqx start

启动错误日志将输出在 log/ 目录。

*EMQ X Edge* 消息服务器进程状态查询:

.. code-block:: bash

    ./bin/emqx_ctl status

正常运行状态，查询命令返回:

.. code-block:: bash

    $ ./bin/emqx_ctl status
    Node 'emqx@127.0.0.1' is started
    emqx 3.1 is running

*EMQ X Edge* 消息服务器提供了状态监控 URL::

    http://localhost:8080/status

停止服务器::

    ./bin/emqx stop

.. _install_via_rpm:

----------
RPM 包安装
----------

EMQ X Edge 3.1版本仅支持 CentOS7 RPM 程序包:

+-------------+--------------------------------------------------+
| CentOS7     | emqx-edge-centos7-v3.1.x86_64.rmp                |
+-------------+--------------------------------------------------+

安装包命名由平台、版本、操纵系统位数组成。

RPM 包安装后可通过操作系统，直接管理启停 EMQ X 服务。

RPM 安装
--------

.. code-block:: console

    rpm -ivh emqx-edge-centos7-v3.1.x86_64.rmp 

配置文件
--------

EMQ X 配置文件: /etc/emqx/emqx.conf，插件配置文件: /etc/emqx/plugins/\*.conf。

日志文件
--------

日志文件目录: /var/log/emqx

数据文件
--------

数据文件目录：/var/lib/emqx/

启动停止
--------

.. code-block:: console

    systemctl start|stop|restart emqx.service

.. _install_via_deb:

----------
DEB 包安装
----------

EMQ X Linux DEB 程序包:

+-------------+---------------------------------------------------+
| Raspbian8   | emqx-edge-raspbian8-v3.1-beta1_armhf.deb          |
+-------------+---------------------------------------------------+
| Raspbian9   | emqx-edge-raspbian9-v3.1-beta1_armhf.deb          |
+-------------+---------------------------------------------------+

安装包命名由平台、版本、操纵系统位数组成，如emqx-edge-raspbian8-v3.1-beta1_armhf.deb。

.. code-block:: console

    sudo dpkg -i emqx-edge-raspbian8-v3.1-beta1_armhf.deb 

配置文件
--------

EMQ X 配置文件: /etc/emqx/emqx.conf，插件配置文件: /etc/emqx/plugins/\*.conf。

日志文件
--------

日志文件目录: /var/log/emqx

数据文件
--------

数据文件目录：/var/lib/emqx/

启动停止
--------

.. code-block:: console

    service emqx start|stop|restart



.. _install_via_docker_image:

---------------
Docker 镜像安装
---------------

+-------------------------------------------+
| emqx-edge-docker-v3.1-beta1.zip           |
+-------------------------------------------+
| emqx-edge-docker-v3.1-beta1-amd64.zip     |
+-------------------------------------------+
| emqx-edge-docker-v3.1-beta1-arm64v8.zip   |
+-------------------------------------------+

从 https://www.emqx.io/downloads/edge 下载*EMQ X Edge* 3.1 Docker 镜像包: 

解压镜像包::

    unzip emqx-edge-docker-v3.1-beta1.zip

加载镜像::

    docker load < emqx-edge-docker-v3.1-beta1

启动容器::

    docker run -tid --name emq31 -p 1883:1883 -p 8083:8083 -p 8883:8883 -p 8084:8084 -p 18083:18083 emqx-docker-v3.0

停止容器::

    docker stop emq31

开启容器::

    docker start emq31

进入 Docker 控制台::

    docker exec -it emq31 /bin/sh

.. _build_from_source:

------------
源码编译安装
------------

*EMQ X Edge* 消息服务器基于 Erlang/OTP 平台开发，项目托管的 GitHub 管理维护，源码编译依赖 Erlang 环境和 git 客户端。

.. NOTE:: EMQ X R3.1 依赖 Erlang R21+ 版本

Erlang 安装: http://www.erlang.org/

Git 客户端: http://www.git-scm.com/

Ubuntu 平台可通过 apt-get 命令安装，CentOS/RedHat 平台可通过 yum 命令安装，Mac 下可通过 brew 包管理命令安装，Windows 下... :(

编译环境准备好之后，clone 代码开始编译:

.. code-block:: bash

    git clone -b emqx30 https://github.com/emqx/emqx-rel.git

    cd emq-relx && make

    cd _rel/emqx && ./bin/emqx console

编译成功后，可执行程序包在目录::

    _rel/emqx

控制台启动编译的 EMQ 程序包::

    cd _rel/emqx && ./bin/emqx console


.. _tcp_ports:

----------------
TCP 服务端口占用
----------------

*EMQ X* R3.0 消息服务器默认占用的 TCP 端口包括:

+-----------+-----------------------------------+
| 1883      | MQTT 协议端口                     |
+-----------+-----------------------------------+
| 8883      | MQTT/SSL 端口                     |
+-----------+-----------------------------------+
| 8083      | MQTT/WebSocket 端口               |
+-----------+-----------------------------------+
| 8080      | HTTP API 端口                     |
+-----------+-----------------------------------+

*EMQ X Edge* R3.1 占用的上述端口，可通过 etc/emqx.conf 配置文件的 'listener' 段落设置:

.. code-block:: properties

    ## TCP Listener: 1883, 127.0.0.1:1883, ::1:1883
    listener.tcp.external = 0.0.0.0:1883

    ## SSL Listener: 8883, 127.0.0.1:8883, ::1:8883
    listener.ssl.external = 8883

    ## External MQTT/WebSocket Listener
    listener.ws.external = 8083

    ## HTTP Management API Listener
    listener.api.mgmt = 127.0.0.1:8080

通过注释或删除相关段落，可禁用相关 TCP 服务启动。


.. _quick_setup:

--------
快速设置
--------

*EMQ X* 消息服务器主要配置文件:

+----------------------+-----------------------------------+
| etc/emqx.conf        | EMQ 消息服务器参数设置            |
+----------------------+-----------------------------------+
| etc/plugins/\*.conf  | EMQ 插件配置文件                  |
+----------------------+-----------------------------------+

etc/emqx.conf 中两个重要的虚拟机启动参数:

+-----------------------+------------------------------------------------------------------+
| node.process_limit    | Erlang 虚拟机允许的最大进程数，EMQ 一个连接会消耗2个Erlang进程   |
+-----------------------+------------------------------------------------------------------+
| node.max_ports        | Erlang 虚拟机允许的最大 Port 数量，EMQ 一个连接消耗1个 Port      |
+-----------------------+------------------------------------------------------------------+

.. NOTE:: Erlang 的 Port 非 TCP 端口，可以理解为文件句柄。

node.process_limit = 参数值 > 最大允许连接数 * 2

node.max_ports = 参数值 > 最大允许连接数

.. WARNING:: 实际连接数量超过 Erlang 虚拟机参数设置，会引起 EMQ 消息服务器宕机!

etc/emqx.conf 配置文件的 `listener` 段落设置最大允许连接数:

.. code-block:: properties

    listener.tcp.external = 0.0.0.0:1883

    listener.tcp.external.acceptors = 8

    listener.tcp.external.max_clients = 1024

*EMQ X* R3.0 消息服务器详细设置，请参见文档: :ref:`config`

.. NOTE::

    ## erlexec: HOME must be set
    uncomment '# export HOME=/root' if "HOME must be set" error.


