## Configuration 

The main configuration files of the *EMQ X Edge* broker are under 'etc/' folder: 

File               |  Description                    
-------------------|---------------------------------
etc/emqx.conf      |  *EMQ X* 3.0 Configuration File 
etc/acl.conf       |  The default ACL File           
etc/plugins/*.conf |  Config Files of Plugins        



## Note: 

> Please visit [EMQ X Doc](https://docs.emqx.io/en/broker/latest/) for general EMQ X parameters configuration, say environment variables, log level & file, MQTT protocol parameters, MQTT listeners, and etc. Here only EMQ X Edge related parameters list. 

## EMQ X 3.0 Config Syntax 

The *EMQ X* integrated with  cuttlefish  library, and uses a user-friendly  k = v  syntax for configuration file: 
    
    
    ## Node name
    node.name = emqx@127.0.0.1
    ...
    ## Max ClientId Length Allowed.
    mqtt.max_clientid_len = 1024
    ...

The configuration files will be preprocessed and translated to Erlang  app.config  before the *EMQ X* broker started: 
    
    
    ----------------------                                          3.0/schema/*.schema      -------------------
    | etc/emqx.conf      |                   -----------------              \|/              | data/app.config |
    |       +            | --> mergeconf --> | data/app.conf | -->  cuttlefish generate  --> |                 |
    | etc/plugins/*.conf |                   -----------------                               | data/vm.args    |
    ----------------------                                                                   -------------------

\----------------------Bridge Config Parameters ---------------------- 

EMQ X Edge can bridge to the remote broker, which can be deployed on cloud, such as aws, azure and so on. The following parameters configurations take aws as an example. 

Refer to [bridge between EMQ X nodes](https://docs.emqx.io/broker/latest/en/bridge/bridge.html) for detailed bridge tutorial. Config Bridge to a Remote Broker on aws: 

> Upgrade Guide: Since v3.2.0, the built-in bridging function of EMQ X Edge has been move to emqx﹣bridge﹣mqtt plug-in, and the bridging related configuration in the original emqx.conf needs to migrated to etc/plugins/emqx﹣bridge﹣mqtt.conf file. And after the configuration, the plug-in needs to be started to apply the configuration. 
    
    
    ## Bridge address: host:port for remote broker on cloud
    bridge.aws.address = 127.0.0.1:1883
    
    ## Protocol version of the bridge: mqttv3 | mqttv4 | mqttv5
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
    bridge.aws.cacertfile = etc/certs/cacert.pem
    
    ## Edge client ssl Certfile of the bridge
    bridge.aws.certfile = etc/certs/client-cert.pem
    
    ## Edge client ssl Keyfile of the bridge
    bridge.aws.keyfile = etc/certs/client-key.pem
    
    ## SSL Ciphers used by the bridge
    bridge.aws.ciphers = ECDHE-ECDSA-AES256-GCM-SHA384,ECDHE-RSA-AES256-GCM-SHA384
    
    ## Ping interval (keepalive) of a down bridge
    bridge.aws.keepalive = 60s
    
    ## TLS versions used by the bridge, seperated by ','
    bridge.aws.tls_versions = tlsv1.2,tlsv1.1,tlsv1
    
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
    ####     used for internal message passing optimization as the underlying MQTT
    ####     protocol does not supports batching. In this case please use the default value.
    bridge.aws.queue.batch_size = 32
    
    ## Base directory for replayq to store messages on disk.
    ## If this config entry is missing or set to undefined, replayq works in a mem-only manner.
    bridge.aws.queue.replayq_dir = data/emqx_aws_bridge/
    
    ## Replayq segment size
    bridge.aws.queue.replayq_seg_bytes = 10MB

## Plugins' Etc Folder 
    
    
    ## Dir of plugins' config
    mqtt.plugins.etc_dir = etc/plugins/
    
    ## File to store loaded plugin names.
    mqtt.plugins.loaded_file = data/loaded_plugins

## Plugin Configuration Files 

File                                |  Description                          
------------------------------------|---------------------------------------
etc/plugins/emqx_auth_username.conf |  Username/Password Auth Plugin Config 
etc/plugins/emqx_auth_clientid.conf |  ClientId Auth Plugin Config          
etc/plugins/emqx_auth_http.conf     |  HTTP Auth/ACL Plugin Config          
etc/plugins/emqx_auth_mysql.conf    |  MySQL Auth/ACL Plugin Config         
etc/plugins/emqx_coap.conf          |  CoAP Protocol Plugin Config          
etc/plugins/emqx_retainer.conf      |  Retainer Plugin Config               
etc/plugins/emqx_web_hook.conf      |  Web Hook Plugin Config               
etc/plugins/emqx_recon.conf         |  Recon Plugin Config                  
etc/plugins/emqx_sn.conf            |  MQTT-SN Protocal Plugin Config       
etc/plugins/emqx_stomp.conf         |  Stomp Protocl Plugin Config          


