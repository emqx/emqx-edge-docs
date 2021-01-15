## 程序安装 (Installation) 

*EMQ X Edge* 消息服务器支持 CentOS、Alpine、Raspbian、Debian、Ubuntu和 Docker 镜像安装。 

## 下载安装 

下载地址: [https://www.emqx.cn/downloads#edge](https://www.emqx.cn/downloads#edge)

安装包命名由平台、版本组成，例如: emqx-edge-centos8-4.2.2-x86_64.zip 

以 CentOS 平台为例，下载安装过程: 
    
    
    unzip emqx-edge-centos8-4.2.2-x86_64.zip

## 程序启动 

控制台调试模式启动，检查 *EMQ X Edge* 是否可正常启动: 
    
    
    cd emqx && ./bin/emqx console

*EMQ X Edge* 消息服务器如启动正常，控制台输出: 
    
    
    Starting emqx on node emqx@127.0.0.1
    Start http:management listener on 8081 successfully.
    Start http:dashboard listener on 18083 successfully.
    Start mqtt:tcp listener on 127.0.0.1:11883 successfully.
    Start mqtt:tcp listener on 0.0.0.0:1883 successfully.
    Start mqtt:ws listener on 0.0.0.0:8083 successfully.
    Start mqtt:ssl listener on 0.0.0.0:8883 successfully.
    Start mqtt:wss listener on 0.0.0.0:8084 successfully.
    EMQ X Edge 4.2.2 is running now!

CTRL+C 关闭控制台。 

守护进程模式启动: 
    
    
    ./bin/emqx start

启动错误日志将输出在 log/ 目录。 

*EMQ X Edge* 消息服务器进程状态查询: 
    
    
    ./bin/emqx_ctl status

正常运行状态，查询命令返回: 
    
    
    $ ./bin/emqx_ctl status
    Node 'emqx@127.0.0.1' is started
    emqx edge 4.2.2 is running

*EMQ X Edge* 消息服务器提供了 Web 管理控制台 URL: 
    
    
    http://localhost:18083

停止服务器: 
    
    
    ./bin/emqx stop
