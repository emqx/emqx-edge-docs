# 扩展插件 (Plugins)

EMQ X Edge 物联网边缘消息服务器通过模块注册和钩子(Hooks)机制，支持用户开发扩展插件定制服务器认证鉴权与业务功能。

EMQ X Edge 3.1 版本官方提供的插件包括:

| 插件                                                                   | 说明                    |
| ---------------------------------------------------------------------- | ----------------------- |
| [ emqx_auth_clientid ](https://github.com/emqx/emqx-auth-clientid)     | ClientId 认证插件       |
| [ emqx_auth_username ](https://github.com/emqx/emqx-auth-username)     | 用户名、密码认证插件    |
| [ emqx_auth_http ](https://github.com/emqx/emqx-auth-http)             | HTTP 认证/访问控制      |
| [ emqx_auth_mysql ](https://github.com/emqx/emqx-auth-mysql)           | MySQL 认证/访问控制     |
| [ emqx_web_hook ](https://github.com/emqx/emqx-web-hook)               | Web Hook 插件           |
| [ emqx_retainer ](https://github.com/emqx/emqx-retainer)               | Retain 消息存储模块     |
| [ emqx_coap ](https://github.com/emqx/emqx-coap)                       | CoAP 协议支持           |
| [ emqx_sn ](https://github.com/emqx/emqx-sn)                           | MQTT-SN 协议支持        |
| [ emqx_stomp ](https://github.com/emqx/emqx-stomp)                     | Stomp 协议支持          |
| [ emqx_recon ](https://github.com/emqx/emqx-recon)                     | Recon 性能调试          |
| [ emqx_reloader ](https://github.com/emqx/emqx-reloader)               | Reloader 代码热加载插件 |
| [ emqx_plugin_template ](https://github.com/emqx/emqx-plugin-template) | 插件开发模版            |

## emqx_retainer Retainer 模块插件

Retainer 模块负责持久化 MQTT Retained 消息: [ https://github.com/emqx/emqx-retainer ](https://github.com/emqx/emqx-retainer)

### 配置 Retainer 模块

etc/plugins/emqx_retainer.conf:

    ## disc: disc_copies, ram: ram_copies
    ## Notice: retainer's storage_type on each node in a cluster must be the same!
    retainer.storage_type = disc

    ## Max number of retained messages
    retainer.max_message_num = 1000000

    ## Max Payload Size of retained message
    retainer.max_payload_size = 64KB

    ## Expiry interval. Never expired if 0
    ## h - hour
    ## m - minute
    ## s - second
    retainer.expiry_interval = 0

### 加载 Retainer 模块

Retainer 模块默认加载。

## emqx_auth_clientid - ClientID 认证插件

ClientId 认证插件: [ https://github.com/emqx/emqx-auth-clientid ](https://github.com/emqx/emqx-auth-clientid)

### ClientID 认证配置

etc/plugins/emqx_auth_clientid.conf:

    ##auth.client.$N.clientid = clientid
    ##auth.client.$N.password = passwd

    ## Examples
    ##auth.client.1.clientid = id
    ##auth.client.1.password = passwd
    ##auth.client.2.clientid = dev:devid
    ##auth.client.2.password = passwd2
    ##auth.client.3.clientid = app:appid
    ##auth.client.3.password = passwd3

### 加载 ClientId 认证插件

    ./bin/emqx_ctl plugins load emqx_auth_clientid

## emqx_auth_username - 用户名密码认证插件

用户名认证插件: [ https://github.com/emqx/emqx-auth-username ](https://github.com/emqx/emqx-auth-username)

### 用户名认证配置

etc/plugins/emqx_auth_username.conf:

    ##auth.user.$N.username = admin
    ##auth.user.$N.password = public

    ## Examples:
    ##auth.user.1.username = admin
    ##auth.user.1.password = public
    ##auth.user.2.username = feng@emqx.io
    ##auth.user.2.password = public

两种方式添加用户:

1. 直接在 etc/plugins/emqx_auth_username.conf 中明文配置默认用户例如:

       auth.username.test = public

2. 通过 './bin/emqx_ctl' 管理命令行添加用户:

       $ ./bin/emqx_ctl users add \<Username> \<Password>

### 加载用户名认证插件

    ./bin/emqx_ctl plugins load emqx_auth_username

## emqx_auth_http: HTTP 认证/访问控制插件

HTTP 认证/访问控制插件: [ https://github.com/emqx/emqx-auth-http ](https://github.com/emqx/emqx-auth-http)

### HTTP 认证插件配置

etc/plugins/emqx_auth_http.conf:

    ## Variables: %u = username, %c = clientid, %a = ipaddress, %P = password, %t = topic

    auth.http.auth_req = http://127.0.0.1:8080/mqtt/auth
    auth.http.auth_req.method = post
    auth.http.auth_req.params = clientid=%c,username=%u,password=%P

    auth.http.super_req = http://127.0.0.1:8080/mqtt/superuser
    auth.http.super_req.method = post
    auth.http.super_req.params = clientid=%c,username=%u

    ## 'access' parameter: sub = 1, pub = 2
    auth.http.acl_req = http://127.0.0.1:8080/mqtt/acl
    auth.http.acl_req.method = get
    auth.http.acl_req.params = access=%A,username=%u,clientid=%c,ipaddr=%a,topic=%t

### HTTP 认证/鉴权 API

认证/ACL 成功，API 返回 200

认证/ACL 失败，API 返回 4xx

### 加载 HTTP 认证插件

    ./bin/emqx_ctl plugins load emqx_auth_http

## emqx_auth_mysql: MySQL 认证/访问控制插件

MySQL 认证/访问控制插件，基于 MySQL 库表认证鉴权: [ https://github.com/emqx/emqx-auth-mysql ](https://github.com/emqx/emqx-auth-mysql)

### MQTT 用户表

    CREATE TABLE `mqtt_user` (
      `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
      `username` varchar(100) DEFAULT NULL,
      `password` varchar(100) DEFAULT NULL,
      `salt` varchar(35) DEFAULT NULL,
      `is_superuser` tinyint(1) DEFAULT 0,
      `created` datetime DEFAULT NULL,
      PRIMARY KEY (`id`),
      UNIQUE KEY `mqtt_username` (`username`)
    ) ENGINE=MyISAM DEFAULT CHARSET=utf8;

::: tip Tip
MySQL 插件可使用系统自有的用户表，通过 'authquery' 配置查询语句。
:::

### MQTT 访问控制表

    CREATE TABLE `mqtt_acl` (
      `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
      `allow` int(1) DEFAULT NULL COMMENT '0: deny, 1: allow',
      `ipaddr` varchar(60) DEFAULT NULL COMMENT 'IpAddress',
      `username` varchar(100) DEFAULT NULL COMMENT 'Username',
      `clientid` varchar(100) DEFAULT NULL COMMENT 'ClientId',
      `access` int(2) NOT NULL COMMENT '1: subscribe, 2: publish, 3: pubsub',
      `topic` varchar(100) NOT NULL DEFAULT '' COMMENT 'Topic Filter',
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;

    INSERT INTO `mqtt_acl` (`id`, `allow`, `ipaddr`, `username`, `clientid`, `access`, `topic`)
    VALUES
        (1,1,NULL,'$all',NULL,2,'#'),
        (2,0,NULL,'$all',NULL,1,'$SYS/#'),
        (3,0,NULL,'$all',NULL,1,'eq #'),
        (5,1,'127.0.0.1',NULL,NULL,2,'$SYS/#'),
        (6,1,'127.0.0.1',NULL,NULL,2,'#'),
        (7,1,NULL,'dashboard',NULL,1,'$SYS/#');

### 配置 MySQL 认证鉴权插件

etc/plugins/emqx_auth_mysql.conf:

    ## Mysql Server
    auth.mysql.server = 127.0.0.1:3306

    ## Mysql Pool Size
    auth.mysql.pool = 8

    ## Mysql Username
    ## auth.mysql.username =

    ## Mysql Password
    ## auth.mysql.password =

    ## Mysql Database
    auth.mysql.database = mqtt

    ## Variables: %u = username, %c = clientid

    ## Authentication Query: select password only
    auth.mysql.auth_query = select password from mqtt_user where username = '%u' limit 1

    ## Password hash: plain, md5, sha, sha256, pbkdf2
    auth.mysql.password_hash = sha256

    ## %% Superuser Query
    auth.mysql.super_query = select is_superuser from mqtt_user where username = '%u' limit 1

    ## ACL Query Command
    auth.mysql.acl_query = select allow, ipaddr, username, clientid, access, topic from mqtt_acl where ipaddr = '%a' or username = '%u' or username = '$all' or clientid = '%c'

### 加载 MySQL 认证鉴权插件

    ./bin/emqx_ctl plugins load emqx_auth_mysql

## emqx_coap: CoAP 协议插件

CoAP 协议插件，支持 RFC 7252 规范: [ https://github.com/emqx/emqx-coap ](https://github.com/emqx/emqx-coap)

### 配置 CoAP 协议插件

etc/plugins/emqx_coap.conf:

    coap.port = 5683

    coap.keepalive = 120s

    coap.enable_stats = off

### 加载 CoAP 协议插件

    ./bin/emqx_ctl plugins load emqx_coap

### libcoap 客户端

    yum install libcoap

    % coap client publish message
    coap-client -m post -e "qos=0&retain=0&message=payload&topic=hello" coap://localhost/mqtt

## emqx_sn: MQTT-SN 协议插件

MQTT-SN 协议插件，支持 MQTT-SN 网关模式: [ https://github.com/emqx/emqx-sn ](https://github.com/emqx/emqx-sn)

### 配置 MQTT-SN 协议插件

::: tip Tip
默认 MQTT-SN 协议 UDP 端口: 1884
:::

etc/plugins/emqx_sn.conf:

    mqtt.sn.port = 1884

### 加载 MQTT-SN 协议插件

    ./bin/emqx_ctl plugins load emqx_sn

## emqx_stomp: Stomp 协议插件

Stomp 协议插件。支持 STOMP 1.0/1.1/1.2 协议客户端连接 EMQ，发布订阅 MQTT 消息: [ https://github.com/emqx/emqx-stomp ](https://github.com/emqx/emqx-stomp)

### 配置插件

::: tip Tip
Stomp 协议端口: 61613
:::

etc/plugins/emqx_stomp.conf:

    stomp.default_user.login = guest

    stomp.default_user.passcode = guest

    stomp.allow_anonymous = true

    stomp.frame.max_headers = 10

    stomp.frame.max_header_length = 1024

    stomp.frame.max_body_length = 8192

    stomp.listener = 61613

    stomp.listener.acceptors = 4

    stomp.listener.max_clients = 512

### 加载 Stomp 插件

    ./bin/emqx_ctl plugins load emqx_stomp

## emqx_recon: Recon 性能调试插件

[ https://github.com/emqx/emqx-recon ](https://github.com/emqx/emqx-recon) emqx_recon 插件集成 recon 性能调测库，'./bin/emqx_ctl' 命令行注册 recon 命令。

### 配置 Recon 插件

etc/plugins/emqx_recon.conf:

    %% Garbage Collection: 10 minutes
    recon.gc_interval = 600

### 加载 Recon 插件

    ./bin/emqx_ctl plugins load emqx_recon

### recon 插件命令

    ./bin/emqx_ctl recon

    recon memory                 #recon_alloc:memory/2
    recon allocated              #recon_alloc:memory(allocated_types, current|max)
    recon bin_leak               #recon:bin_leak(100)
    recon node_stats             #recon:node_stats(10, 1000)
    recon remote_load Mod        #recon:remote_load(Mod)

## emqx_reloader: 代码热加载插件

[ https://github.com/emqx/emqx-reloader ](https://github.com/emqx/emqx-reloader)

用于开发调试的代码热升级插件。加载该插件后，EMQ 会自动热升级更新代码。

::: tip Tip
产品部署环境不建议使用该插件
:::

### 配置 Reloader 插件

etc/plugins/emqx_reloader.conf:

    reloader.interval = 60

    reloader.logfile = log/reloader.log

### 加载 Reloader 插件

    ./bin/emqx_ctl plugins load emqx_reloader

### Reloader 插件命令

    ./bin/emqx_ctl reload

    reload \<Module>             # Reload a Module

## EMQ X R3.0 插件开发

### 创建插件项目

参考 [ emqx_plugin_template ](https://github.com/emqx/emqx-plugin-template) 插件模版创建新的插件项目。

### 注册认证/访问控制模块

认证演示模块 - emqx_auth_demo.erl

    -module(emqx_auth_demo).

    -behaviour(emqx_auth_mod).

    -include_lib("emqx/include/emqx.hrl").

    -export([init/1, check/3, description/0]).

    init(Opts) -> {ok, Opts}.

    check(#{client_id := ClientId, username := Username}, Password, _Opts) ->
        io:format("Auth Demo: clientId=~p, username=~p, password=~p~n", [ClientId, Username, Password]),
        ok.

    description() -> "Auth Demo Module".

访问控制演示模块 - emqx_acl_demo.erl

    -module(emqx_acl_demo).

    -include_lib("emqx/include/emqx.hrl").

    %% ACL callbacks
    -export([init/1, check_acl/2, reload_acl/1, description/0]).

    init(Opts) ->
        {ok, Opts}.

    check_acl({Credentials, PubSub, Topic}, _Opts) ->
        io:format("ACL Demo: ~p ~p ~p~n", [Credentials, PubSub, Topic]),
        allow.

    reload_acl(_Opts) ->
        ok.

    description() -> "ACL Demo Module".

注册认证、访问控制模块 - emqx_plugin_template_app.erl

    ok = emqx_access_control:register_mod(auth, emqx_auth_demo, []),
    ok = emqx_access_control:register_mod(acl, emqx_acl_demo, []),

### 注册扩展钩子(Hooks)

通过钩子(Hook)处理客户端上下线、主题订阅、消息收发。

emqx_plugin_template.erl:

    %% Called when the plugin application start
    load(Env) ->
        %% Hook with function
        emqx:hook('client.connected',    fun ?MODULE:on_client_connected/4, [Env]),
        emqx:hook('client.disconnected', fun ?MODULE:on_client_disconnected/3, [Env]),
        %% Hook with MFA
        emqx:hook('client.subscribe',    {?MODULE, on_client_subscribe, [Env]}),
        emqx:hook('client.unsubscribe',  {?MODULE, on_client_unsubscribe, [Env]}),
        emqx:hook('session.created',     {?MODULE, on_session_created, [Env]}),
        emqx:hook('session.resumed',     {?MODULE, on_session_resumed, [Env]}),
        emqx:hook('session.subscribed',  {?MODULE, on_session_subscribed, [Env]}),
        emqx:hook('session.unsubscribed',{?MODULE, on_session_unsubscribed, [Env]}),
        emqx:hook('session.terminated',  {?MODULE, on_session_terminated, [Env]}),
        emqx:hook('message.publish',     {?MODULE, on_message_publish, [Env]}),
        emqx:hook('message.delivered',   {?MODULE, on_message_delivered, [Env]}),
        emqx:hook('message.acked',       {?MODULE, on_message_acked, [Env]}),
        emqx:hook('message.dropped',     {?MODULE on_message_dropped, [Env]}).

扩展钩子(Hook):

| 钩子                 | 说明               |
| -------------------- | ------------------ |
| client.connected     | 客户端上线         |
| client.disconnected  | 客户端连接断开     |
| client.subscribe     | 客户端订阅主题     |
| client.unsubscribe   | 客户端取消订阅主题 |
| session.created      | 会话创建           |
| session.resumed      | 会话恢复           |
| session.subscribed   | 会话订阅主题后     |
| session.unsubscribed | 会话取消订阅主题后 |
| session.terminated   | 会话终止           |
| message.publish      | MQTT 消息发布      |
| message.delivered    | MQTT 消息送达      |
| message.acked        | MQTT 消息回执      |
| message.dropped      | MQTT 消息丢弃      |

### 注册扩展命令行

扩展命令行演示模块 - emqx_cli_demo.erl

    -module(emqx_cli_demo).

    -export([cmd/1]).

    cmd(["arg1", "arg2"]) ->
        emqx_cli:print("ok");

    cmd(_) ->
        emqx_cli:usage([{"cmd arg1 arg2", "cmd demo"}]).

注册命令行模块 - emqx_plugin_template_app.erl

    ok = emqx_ctl:register_command(cmd, {emqx_cli_demo, cmd}, []),

插件加载后，'./bin/emqx_ctl'新增命令行:

    ./bin/emqx_ctl cmd arg1 arg2

### 插件配置文件

插件自带配置文件放置在 etc/${plugin_name}.conf|config， EMQ X 支持两种插件配置格式:

1. ${plugin_name}.config，Erlang 原生配置文件格式:


       [
         {plugin_name, [
           {key, value}
         ]}
       ].

2. ${plugin_name}.conf, sysctl 的 k = v 通用格式:


       plugin_name.key = value

::: tip Tip
k = v 格式配置需要插件开发者创建 priv/plugin_name.schema 映射文件。
:::

### 编译发布插件

1. clone emqx-rel 项目:


       git clone https://github.com/emqx/emqx-rel.git

2. Makefile 增加 `DEPS`:


       DEPS += plugin_name
       dep_plugin_name = git url_of_plugin

3. relx.config 中 release 段落添加:


       {plugin_name, load},
