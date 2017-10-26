---
layout: post
title: "RabbitMQ简介和安装"
date: 2017-10-26 
description: "RabbitMQ系统架构，以及linux安装RabbitMQ"
tag: [消息机制, RabbitMQ]

---
## 正文：
RabbitMQ是一个开源的AMQP实现，服务器端用Erlang语言编写，支持多种客户端，如：Python、Ruby、.NET、Java、JMS、C、PHP、ActionScript、XMPP、STOMP等，支持AJAX。用于在分布式系统中存储转发消息，在易用性、扩展性、高可用性等方面表现不俗。  
AMQP，即Advanced message Queuing Protocol，高级消息队列协议，是应用层协议的一个开放标准，为面向消息的中间件设计。消息中间件主要用于组件之间的解耦，消息的发送者无需知道消息使用者的存在，反之亦然。  
AMQP的主要特征是面向消息、队列、路由（包括点对点和发布/订阅）、可靠性、安全。

### RabbitMQ系统架构
RabbitMQ Server： 也叫broker server，是一种传输服务，负责维护一条从Producer到consumer的路线，保证数据能够按照指定的方式进行传输。
![rabbitmq.jpg-79.4kB][1]
Producer，数据的发送方。  
Consumer，数据的接收方。  
Exchanges， 接收消息，转发消息到绑定的队列。主要使用3种类型：direct， topic， fanout。  
Queue， RabbitMQ内部存储消息的对象。相同属性的queue可以重复定义，但只有第一次定义的有效。  
Bindings， 绑定Exchanges和Queue之间的路由。  
Connection， 就是一个TCP的连接。Producer和consumer都是通过TCP连接到RabbitMQ Server的。  
Channel，虚拟连接。它建立在上述的TCP连接中。数据流动都是在Channel中进行的。也就是说，一般情况是程序起始建立TCP连接，第二步就是建立这个Channel。

### RabbitMQ安装

Ubuntu 安装  
编辑 /etc/apt/sources.list 文件添加如下行  
deb http://www.rabbitmq.com/debian/ testing main  
执行更新软件源命令  
#apt-get update.  
安装rabbitmq  
#apt-get install rabbitmq-server  
启动rabbitmq  
#service rabbitmq-server start

Centos 安装  
从官网下载erlang和rabbitmq 的rpm包  
安装erlang  
#rpm –i erlang-17.4-1.el6.x86_64.rpm  
安装rabbitmq  
#rpm –i rabbitmq-server-3.5.3-1.noarch.rpm  
rabbitmq的rpm安装包不能指定安装路径

### RabbitMQ配置文件  
RABBITMQ配置文件位置：  
Generic UNIX- $RABBITMQ_HOME/etc/rabbitmq/  
Debian- /etc/rabbitmq/  
RPM- /etc/rabbitmq/

### RabbitMQ页面（web）管理  
启用web插件  
#rabbitmq-plugins enable rabbitmq_management #启用

关闭web插件  
#rabbitmq-plugins disable rabbitmq_management  
可以用默认账号guest,guest登陆http://主机IP:15672，如果要远程登录，需要先创建帐户


### RabbitMQ创建帐户  
如果是集群的话，只要在一台主机设置即可，其它会自动同步。  
#rabbitmqctl add_user user_name user_password  –user_name为新建的用户，user_password为密码  
#rabbitmqctl  set_user_tags user_name administrator –将用户设置为管理员角色  
#rabbitmqctl set_permissions -p / user_name “.*” “.*” “.*”  
–在 / 虚拟主机里设置iom用户配置权限，写权限，读权限。.*是正则表达式里用法。rabbitmq的权限是根据不同的虚拟主机（virtual hosts）配置的，同用户在不同的虚拟主机（virtual hosts）里可能不一样。


  [1]: http://static.zybuluo.com/jiangtaibao/cp2oczj7p1rqa8luhc49m8ep/rabbitmq.jpg