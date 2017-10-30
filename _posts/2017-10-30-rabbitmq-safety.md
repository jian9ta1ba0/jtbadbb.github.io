---
layout: post
title: "RabbitMQ安全机制"
date: 2017-10-30
description: "RabbitMQ消息确认以及持久化"
tag: [消息机制, RabbitMQ]

---
## 正文：
### publish消息确认机制

如果采用标准的 AMQP 协议，则唯一能够保证消息不会丢失的方式是利用事务机制 — 令 channel 处于 transactional 模式、向其 publish 消息、执行 commit 动作。在这种方式下，事务机制会带来大量的多余开销，并会导致吞吐量下降 250% 。为了补救事务带来的问题，引入了 confirmation 机制（即 Publisher Confirm）。  
confirm 机制是在channel上使用 confirm.select方法，处于 transactional 模式的 channel 不能再被设置成 confirm 模式，反之亦然。  
在 channel 被设置成 confirm 模式之后，所有被 publish 的后续消息都将被 confirm（即 ack） 或者被 nack 一次。但是没有对消息被 confirm 的快慢做任何保证，并且同一条消息不会既被 confirm 又被 nack 。  
RabbitMQ 将在下面的情况中对消息进行 confirm ：  
（1）RabbitMQ发现当前消息无法被路由到指定的 queues 中；  
（2）非持久属性的消息到达了其所应该到达的所有 queue 中（和镜像 queue 中）；  
（3）持久消息到达了其所应该到达的所有 queue 中（和镜像 queue 中），并被持久化到了磁盘（被 fsync）；  
（4）持久消息从其所在的所有 queue 中被 consume 了（如果必要则会被 acknowledge）。

###  consumer消息确认机制
为了保证数据不被丢失，RabbitMQ支持消息确认机制，即acknowledgments。  
如果没启动消息确认机制，RabbitMQ在consumer收到消息后就会把消息删除。  
启用消息确认后，consumer在处理数据后应通过回调函数显示发送ack， RabbitMQ收到ack后才会删掉数据。如果consumer一段时间内不回馈，RabbitMQ会将该消息重新分配给另外一个绑定在该队列上的consumer。另一种情况是consumer断开连接，但是获取到的消息没有回馈，则RabbitMQ同样重新分配。  
  
  注意：如果consumer 没调用basic.qos 方法设置prefetch_count=1，那即使该consumer有未ack的messages，RabbitMQ仍会继续发messages给它。
  
  ### 消息持久化
消息确认机制确保了consumer退出时消息不会丢失，但如果是RabbitMQ本身因故障退出，消息还是会丢失。为了保证在RabbitMQ出现意外情况时数据仍没有丢失，需要将queue和message都要持久化。  
queue持久化：  
    channel.queue_declare(queue=“hello”, durable=True)
  
message持久化：  
    channel.basic_publish(exchange=“”,routing_key=”task_queue”,body=message,properties=pika.BasicProperties(delivery_mode = 2,)  #消息持久化)

即使有消息持久化，数据也有可能丢失，因为rabbitmq是先将数据缓存起来，到一定条件才保存到硬盘上，这期间rabbitmq出现意外数据有可能丢失。

网上有测试表明：持久化会对RabbitMQ的性能造成比较大的影响，可能会下降10倍不止。
