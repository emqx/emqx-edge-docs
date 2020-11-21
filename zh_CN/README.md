# EMQ X Edge 物联网边缘消息服务器

EMQ X Edge 是轻量级开源消息服务器，可运行在资源受限的边缘设备上。它是连接云端和本地设备之间的桥梁，作为物联网平台体系中的边缘计算节点上运行的消息代理，在边缘节点上使用业务规则引擎将大部分数据进行本地处理，也能够实时响应设备端的请求。在 EMQ X 边缘节点与后台的 EMQ X 服务器处理集群之间采用加密的传输协议，如果 Edge 节点和远程 EMQ 之间由于传输网络问题导致连接断开，Edge 节点会自动保存指定的消息到本地的存储，并且在网络正常之后会自动将数据发送到远程的 EMQ X 集群。

EMQ X Edge 完整支持 MQTT V3.1/V3.1.1/V5.0 版本协议规范，并支持 MQTT-SN 、TCP、WebSocket、CoAP、Stomp，以及 Modbus 等工业协议。

![image](./_static/edge-overview1.png)

## EMQ X Edge R3.1 项目文档目录:

- [开始使用](getstarted.md)
- [程序安装](install.md)
- [配置说明](config.md)
- [扩展插件](plugins.md)

EMQ X 项目支持与联系:

| 官网:    | [ http://emqtt.com ](http://emqtt.com)                         |
| -------- | -------------------------------------------------------------- |
| 项目:    | [ https://github.com/emqx/emqx ](https://github.com/emqx/emqx) |
| 微信:    | emqttd                                                         |
| 微博:    | [ http://weibo.com/emqtt ](http://weibo.com/emqtt)             |
| Twitter: | @emqtt                                                         |
| 公司:    | 杭州映云科技有限公司                                           |
| 联系:    | contact@emqx.io                                                |

![image](./_static/emq.jpg)
