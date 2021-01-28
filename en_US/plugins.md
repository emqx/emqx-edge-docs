# Plugins 

The *EMQ X Edge* broker could be extended by plugins. Users could develop plugins to customize authentication, ACL and functions of the broker, or integrate the broker with other systems. 

The plugins that *EMQ X Edge* 3.0 released: 

Plugin                                                             |  Description                    
-------------------------------------------------------------------|---------------------------------
[ emqx_auth_clientid ](https://github.com/emqx/emqx-auth-clientid) |  ClientId Auth Plugin           
[ emqx_auth_username ](https://github.com/emqx/emqx-auth-username) |  Username/Password Auth Plugin  
[ emqx_auth_http ](https://github.com/emqx/emqx-auth-http)         |  HTTP Auth/ACL Plugin           
[ emqx_auth_mysql ](https://github.com/emqx/emqx-auth-mysql)       |  MySQL Auth/ACL Plugin          
[ emqx_web_hook ](https://github.com/emqx/emqx-web-hook)           |  Web Hook Plugin                
[ emqx_lua_hook ](https://github.com/emqx/emqx-lua-hook)           |  Lua Hook Plugin                
[ emqx_retainer ](https://github.com/emqx/emqx-retainer)           |  Retained Messages Store Plugin 
[ emqx_coap ](https://github.com/emqx/emqx-coap)                   |  CoAP Protocol Plugin           
[ emqx_sn ](https://github.com/emqx/emqx-sn)                       |  MQTT-SN Protocol Plugin        
[ emqx_stomp ](https://github.com/emqx/emqx-stomp)                 |  STOMP Protocol Plugin          
[ emqx_recon ](https://github.com/emqx/emqx-recon)                 |  Recon Plugin                   
[ emqx_reloader ](https://github.com/emqx/emqx-reloader)           |  Reloader Plugin                



## emqx_auth_clientid - ClientID Auth Plugin 

ClientID Auth Plugin: [ https://github.com/emqx/emqx-auth-clientid ](https://github.com/emqx/emqx-auth-clientid)

### Configure ClientID Auth Plugin 

etc/plugins/emqx_auth_clientid.conf:   
```
##auth.client.$N.clientid = clientid
##auth.client.$N.password = passwd
    
## Examples
##auth.client.1.clientid = id
##auth.client.1.password = passwd
##auth.client.2.clientid = dev:devid
##auth.client.2.password = passwd2
##auth.client.3.clientid = app:appid
##auth.client.3.password = passwd3
```

## Load ClientId Auth Plugin 
```bash
./bin/emqx_ctl plugins load emqx_auth_clientid
```

## emqx_auth_username - Username Auth Plugin 

Username Auth Plugin: [ https://github.com/emqx/emqx-auth-username ](https://github.com/emqx/emqx-auth-username)

### Configure Username Auth Plugin 

etc/plugins/emqx_auth_username.conf: 
    
```
##auth.user.$N.username = admin
##auth.user.$N.password = public
    
## Examples:
##auth.user.1.username = admin
##auth.user.1.password = public
##auth.user.2.username = feng@emqx.io
##auth.user.2.password = public
```
Add username/password by  ./bin/emqx_ctl users  CLI: 
```bash
$ ./bin/emqx_ctl users add \<Username> \<Password>
```
or by configuring `etc/plugins/emqx_auth_username.conf`: 
```
auth.username.test = public
```

### Load Username Auth Plugin 
    
```bash
./bin/emqx_ctl plugins load emqx_auth_username
```

------------------------------------emqx_auth_http - HTTP Auth/ACL Plugin ------------------------------------ 

MQTT Authentication/ACL with HTTP API: [ https://github.com/emqx/emqx-auth-http ](https://github.com/emqx/emqx-auth-http)

### Configure HTTP Auth/ACL Plugin 

etc/plugins/emqx_auth_http.conf: 
    
    
## Variables: %u = username, %c = clientid, %a = ipaddress, %P = password, %t = topic

```
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
```

### HTTP Auth/ACL API 

Return 200 if ok 

Return 4xx if unauthorized 

### Load HTTP Auth/ACL Plugin 
    
```bash
./bin/emqx_ctl plugins load emqx_auth_http
```

## emqx_auth_mysql - MySQL Auth/ACL Plugin 

MQTT Authentication, ACL against MySQL database: [ https://github.com/emqx/emqx-auth-mysql ](https://github.com/emqx/emqx-auth-mysql)

### MQTT User Table 
```sql
CREATE TABLE `mqtt_user` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `username` varchar(100) DEFAULT NULL,
  `password` varchar(100) DEFAULT NULL,
  `salt` varchar(20) DEFAULT NULL,
  `is_superuser` tinyint(1) DEFAULT 0,
  `created` datetime DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `mqtt_username` (`username`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8;
```

### MQTT ACL Table 

```sql
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
```

### Configure MySQL Auth/ACL Plugin 

etc/plugins/emqx_auth_mysql.conf: 
```
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
```

### Load MySQL Auth/ACL plugin 

```bash
./bin/emqx_ctl plugins load emqx_auth_mysql
```

## emqx_retainer - Retainer Plugin 

Retainer Plugin: [ https://github.com/emqx/emqx-retainer ](https://github.com/emqx/emqx-retainer)

### Configure Retainer Plugin 

etc/plugins/emqx_retainer.conf: 

```
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

## emqx_coap: CoAP Protocol Plugin 
```

CoAP Protocol Plugin: [ https://github.com/emqx/emqx-coap ](https://github.com/emqx/emqx-coap)

### Configure CoAP Plugin 

etc/plugins/emqx_coap.conf: 
```
coap.port = 5683
    
coap.keepalive = 120s
    
coap.enable_stats = off
```

### Load CoAP Protocol Plugin 
```bash
./bin/emqx_ctl plugins load emqx_coap
```

### libcoap Client 
```bash
yum install libcoap

% coap client publish message
coap-client -m post -e "qos=0&retain=0&message=payload&topic=hello" coap://localhost/mqtt
```
## emqx_sn: MQTT-SN Protocol 

MQTT-SN Protocol/Gateway Plugin: [ https://github.com/emqx/emqx-sn ](https://github.com/emqx/emqx-sn)

### Configure MQTT-SN Plugin 

::: tip Tip
UDP Port for MQTT-SN: 1884 
:::

etc/plugins/emqx_sn.conf: 
```
mqtt.sn.port = 1884
```
### Load MQTT-SN Plugin 
```bash
./bin/emqx_ctl plugins load emqx_sn
```
## emqx_stomp - STOMP Protocol Plugin 

STOMP Protocol Plugin: [ https://github.com/emqx/emqx-stomp ](https://github.com/emqx/emqx-stomp)

Support STOMP 1.0/1.1/1.2 clients to connect to *EMQ X* broker and communicate with MQTT Clients. 

### Configure Stomp Plugin 

etc/plugins/emqx_stomp.conf: 

::: tip Tip
Default Port for STOMP Protocol: 61613 
:::

```
stomp.default_user.login = guest
    
stomp.default_user.passcode = guest
    
stomp.allow_anonymous = true
    
stomp.frame.max_headers = 10
    
stomp.frame.max_header_length = 1024
    
stomp.frame.max_body_length = 8192
    
stomp.listener = 61613
    
stomp.listener.acceptors = 4
    
stomp.listener.max_clients = 512
```

### Load Stomp Plugin 
```bash
./bin/emqx_ctl plugins load emqx_stomp
```
## emqx_recon - Recon Plugin 

Recon Plugin: [ https://github.com/emqx/emqx-recon ](https://github.com/emqx/emqx-recon)

The plugin loads [ recon ](https://ferd.github.io/recon/) library on a running *EMQ X* broker. Recon library helps debug and optimize an Erlang application. 

### Configure Recon Plugin 

etc/plugins/emqx_recon.conf: 
```
%% Garbage Collection: 10 minutes
recon.gc_interval = 600
```

### Load Recon Plugin 
```bash
./bin/emqx_ctl plugins load emqx_recon
```
### Recon CLI 
```bash
./bin/emqx_ctl recon
    
recon memory                 #recon_alloc:memory/2
recon allocated              #recon_alloc:memory(allocated_types, current|max)
recon bin_leak               #recon:bin_leak(100)
recon node_stats             #recon:node_stats(10, 1000)
recon remote_load Mod        #recon:remote_load(Mod)
```
## emqx_reloader - Reloader Plugin 

Erlang Module Reloader for Development: [ https://github.com/emqx/emqx-reloader ](https://github.com/emqx/emqx-reloader)

::: tip Tip
Don't load the plugin in production! 
:::

### Configure Reloader Plugin 

etc/plugins/emqx_reloader.conf: 
```
reloader.interval = 60
    
reloader.logfile = log/reloader.log
```
### Load  Reloader  Plugin 
```bash
./bin/emqx_ctl plugins load emqx_reloader
```
### reload CLI 
```bash
./bin/emqx_ctl reload
    
reload \<Module>             # Reload a Module
```
## Plugin Development Guide 

### Create a Plugin Project 

Refer to [ emqx_plugin_template ](https://github.com/emqx/emqx-plugin-template) for new plugin project. 

### Register Auth/ACL Modules 

emqx_auth_demo.erl - Demo Authentication Module: 
    
```
-module(emqx_auth_demo).

-behaviour(emqx_auth_mod).

-include_lib("emqx/include/emqx.hrl").

-export([init/1, check/3, description/0]).

init(Opts) -> {ok, Opts}.

check(#mqtt_client{client_id = ClientId, username = Username}, Password, _Opts) ->
    io:format("Auth Demo: clientId=~p, username=~p, password=~p~n",
              [ClientId, Username, Password]),
    ok.
    
description() -> "Demo Auth Module".
```

emqx_acl_demo.erl - Demo ACL Module: 
```
-module(emqx_acl_demo).
    
-include_lib("emqx/include/emqx.hrl").
    
%% ACL callbacks
-export([init/1, check_acl/2, reload_acl/1, description/0]).
    
init(Opts) ->
    {ok, Opts}.
    
check_acl({Client, PubSub, Topic}, Opts) ->
    io:format("ACL Demo: ~p ~p ~p~n", [Client, PubSub, Topic]),
    allow.
    
reload_acl(_Opts) ->
    ok.
    
description() -> "ACL Module Demo".
```
emqx_plugin_template_app.erl - Register the auth/ACL modules: 
```
ok = emqx_access_control:register_mod(auth, emqx_auth_demo, []),
ok = emqx_access_control:register_mod(acl, emqx_acl_demo, []),
```

### Register Callbacks for Hooks 

The plugin could register callbacks for hooks. The hooks will be run by the broker when a client connected/disconnected, a topic subscribed/unsubscribed or a message published/delivered: 

Name                 |  Description                                           
---------------------|--------------------------------------------------------
client.connected     |  Run when a client connects to the broker successfully 
client.subscribe     |  Run before a client subscribes topics                 
client.unsubscribe   |  Run when a client unsubscribes topics                 
session.subscribed   |  Run after a client subscribes a topic                 
session.unsubscribed |  Run after a client unsubscribes a topic               
message.publish      |  Run when a message is published                       
message.delivered    |  Run when a message is delivered                       
message.acked        |  Run when a message(qos1/2) is acked                   
client.disconnected  |  Run when a client is disconnnected                    



emqx_plugin_template.erl for example: 
```
%% Called when the plugin application start
load(Env) ->
    emqx:hook('client.connected', fun ?MODULE:on_client_connected/3, [Env]),
    emqx:hook('client.disconnected', fun ?MODULE:on_client_disconnected/3, [Env]),
    emqx:hook('client.subscribe', fun ?MODULE:on_client_subscribe/4, [Env]),
    emqx:hook('session.subscribed', fun ?MODULE:on_session_subscribed/4, [Env]),
    emqx:hook('client.unsubscribe', fun ?MODULE:on_client_unsubscribe/4, [Env]),
    emqx:hook('session.unsubscribed', fun ?MODULE:on_session_unsubscribed/4, [Env]),
    emqx:hook('message.publish', fun ?MODULE:on_message_publish/2, [Env]),
    emqx:hook('message.delivered', fun ?MODULE:on_message_delivered/4, [Env]),
    emqx:hook('message.acked', fun ?MODULE:on_message_acked/4, [Env]).
```
### Register CLI Modules 

emqx_cli_demo.erl: 

```
-module(emqx_cli_demo).
    
-include_lib("emqx/include/emqx_cli.hrl").
    
-export([cmd/1]).
    
cmd(["arg1", "arg2"]) ->
    ?PRINT_MSG("ok");
    
cmd(_) ->
    ?USAGE([{"cmd arg1 arg2", "cmd demo"}]).
```

emqx_plugin_template_app.erl - register the CLI module to *EMQ X* broker: 

```
emqx_ctl:register_cmd(cmd, {emqx_cli_demo, cmd}, []).
```

There will be a new CLI after the plugin loaded: 
```bash
./bin/emqx_ctl cmd arg1 arg2
```

### Create Configuration File 

Create  etc/${plugin_name}.conf|config  file for the plugin (which will be put in the directory  etc/plugins/${plugin_name}  after compilation). The *EMQ X* broker supports two types of config syntax: 

  1. ${plugin_name}.config with erlang syntax: 

        ```
        [
        {plugin_name, [
            {key, value}
        ]}
        ].
        ```


  2. ${plugin_name}.conf with a general  k = v  syntax: 

        ```
        plugin_name.key = value
        ```
### Build and Release the Plugin 

  1. clone emqx-rel project: 
        ```bash
        git clone https://github.com/emqx/emqx-rel.git
        ```
  2. Add  DEPS  in Makefile: 

        ```
        DEPS += plugin_name
        dep_plugin_name = git url_of_plugin
        ```
  3. Add the plugin in relx.config: 

        ```
        {plugin_name, load},
        ```
