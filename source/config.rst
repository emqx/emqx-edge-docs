
.. _configuration:

=========================
配置说明 (Configuration)
=========================

--------------------
EMQ X Edge R3.1 配置文件
--------------------

*EMQ X Edge* R3.1 消息服务器通过 etc/ 目录下配置文件进行设置，主要配置文件包括:

+----------------------------+--------------------------------------+
| 配置文件                   | 说明                                 |
+----------------------------+--------------------------------------+
| etc/emqx.conf              | EMQ X Edge 消息服务器配置文件        |
+----------------------------+--------------------------------------+
| etc/acl.conf               | EMQ X Edge 默认ACL规则配置文件       |
+----------------------------+--------------------------------------+
| etc/plugins/\*.conf        | EMQ X Edge 各类插件配置文件          |
+----------------------------+--------------------------------------+

注意:
------
  这里仅列出配置 EMQ X Edge 桥接（bridge）相关的参数，其他参数配置（如环境变量，日志，认证，协议参数等），请参考EMQ X Broker文档 — https://developer.emqx.io/docs/emq/v3/cn/config.html。


---------------------
桥接（Bridges）配置参数
---------------------

*EMQ X Edge* 可以与远程 MQTT Server 桥接，如部署在aws, Azure等公有云，或企业私有云。

这里以桥接到部署在 aws 上的 EMQ X broker为例，详细桥接教程参考[EMQ X 节点之间的桥接](https://docs.emqx.io/tutorial/v3/cn/bridge/emqx_to_emqx.html)。

> 升级指南: v3.2.0 版本起，EMQ X Edge 内置桥接功能迁移到 emqx_bridge_mqtt 插件，原 emqx.conf 中桥接相关配置迁移至 etc/plugins/emqx_bridge_mqtt.conf 文件，配置完成后需启动插件以应用配置。

Bridges to  aws 参数设置
--------------------------

.. code-block:: properties

    ## Bridge address: host:port for remote broker on cloud
    bridge.aws.address = 127.0.0.1:1883

    ## Protocol version of the bridge, Enum: mqttv3 | mqttv4 | mqttv5
    bridge.aws.proto_ver = mqttv4

    ## The ClientId of the Edge 
    bridge.aws.client_id = bridge_aws

    ## The Clean start flag of the Edge: true | false
    bridge.aws.clean_start = true

    ## The username for the edge
    bridge.aws.username = user

    ## The password for the edge
    bridge.aws.password = passwd

    ## Mountpoint of the bridge
    bridge.aws.mountpoint = bridge/aws/${node}/

    ## Topics which will be forwarded to the remote broker，for example, topic1/#
    bridge.aws.forwards = topic1/#,topic2/#

    ## Bribge to remote server via SSL: on | off
    bridge.aws.ssl = off

    ## PEM-encoded CA certificates of the bridge
    ## bridge.aws.cacertfile = etc/certs/cacert.pem

    ## Edge client ssl Certfile of the bridge
    ## bridge.aws.certfile = etc/certs/client-cert.pem

    ## Edge client ssl Keyfile of the bridge
    ## bridge.aws.keyfile = etc/certs/client-key.pem

    ## SSL Ciphers used by the bridge
    ## bridge.aws.ciphers = ECDHE-ECDSA-AES256-GCM-SHA384,ECDHE-RSA-AES256-GCM-SHA384

    ## Ping interval (keepalive) of a down bridge
    bridge.aws.keepalive = 60s

    ## TLS versions used by the bridge, seperated by ','
    ## bridge.aws.tls_versions = tlsv1.2,tlsv1.1,tlsv1

    ## Subscriptions of the bridge topic
    bridge.aws.subscription.1.topic = cmd/topic1

    ## qos of the above subscription: 0 | 1 | 2
    bridge.aws.subscription.1.qos = 1

    ## Subscriptions of the bridge topic
    bridge.aws.subscription.2.topic = cmd/topic2

    ## qos of the above subscription: 0 | 1 | 2
    bridge.aws.subscription.2.qos = 1

    ## Start type of the bridg: manual | auto
    bridge.aws.start_type = manual

    ## Bridge reconnection interval
    bridge.aws.reconnect_interval = 30s

    ## Retry interval for bridge QoS1 message delivering
    bridge.aws.retry_interval = 20s

    ## Inflight size
    bridge.aws.max_inflight = 32

    ## Maximum number of messages in one batch when sending to remote borkers
    ## NOTE: when bridging via MQTT connection to remote broker, this config is only
    ##       used for internal message passing optimization as the underlying MQTT
    ##       protocol does not supports batching. In this case please use the default value.
    bridge.aws.queue.batch_size = 32

    ## Base directory for replayq to store messages on disk.
    ## If this config entry is missing or set to undefined, replayq works in a mem-only manner.
    bridge.aws.queue.replayq_dir = data/emqx_aws_bridge/

    ## Replayq segment size
    bridge.aws.queue.replayq_seg_bytes = 10MB


----------------
扩展插件配置文件
----------------

.. code-block:: properties

    ##--------------------------------------------------------------------
    ## The etc dir for plugins' config
    plugins.etc_dir =etc/plugins/

    ## The file to store loaded plugin names
    plugins.loaded_file = data/loaded_plugins

   
*EMQ X Edge* 插件配置文件，默认在 etc/plugins/ 目录，可修改 plugins.etc_dir 来调整目录:

+----------------------------------------+----------------------------------+
| 配置文件                               | 说明                              |
+----------------------------------------+----------------------------------+
| etc/plugins/emqx_retainer.conf         | Retain 消息存储插件               |
+----------------------------------------+----------------------------------+
| etc/plugins/emqx_management.conf       | 管理插件                          |
+----------------------------------------+----------------------------------+
| etc/plugins/emqx_auth_username.conf    | 用户名、密码认证插件                |
+----------------------------------------+----------------------------------+
| etc/plugins/emqx_auth_clientid.conf    | ClientId 认证插件                 |
+----------------------------------------+----------------------------------+
| etc/plugins/emqx_auth_http.conf        | HTTP 认证插件配置                 |
+----------------------------------------+----------------------------------+
| etc/plugins/emqx_auth_mysql.conf       | MySQL 认证插件配置                |
+----------------------------------------+----------------------------------+
| etc/plugins/emqx_web_hook.conf         | Web Hook 插件配置                 |
+----------------------------------------+----------------------------------+
| etc/plugins/emqx_coap.conf             | CoAP 协议服务器配置                |
+----------------------------------------+----------------------------------+
| etc/plugins/emqx_recon.conf            | Recon 调试插件配置                 |
+----------------------------------------+----------------------------------+
| etc/plugins/emqx_reloader.conf         | 热加载插件配置                     |
+----------------------------------------+----------------------------------+
| etc/plugins/emqx_sn.conf               | MQTT-SN 协议插件配置               |
+----------------------------------------+----------------------------------+
| etc/plugins/emqx_bridge_mqtt.conf      | MQTT 桥接插件配置 (3.2.0 以后)      |
+----------------------------------------+----------------------------------+
| etc/plugins/emqx_stomp.conf            | Stomp 协议插件配置                 |
+----------------------------------------+----------------------------------+
| etc/plugins/emqx_auth_jwt.conf         | Jwt 认证插件配置                   |
+----------------------------------------+----------------------------------+
| etc/plugins/emqx_delayed_publish.conf  | 消息延迟发布插件                   |
+----------------------------------------+----------------------------------+

