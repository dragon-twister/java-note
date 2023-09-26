# 消息队列

[TOC]
## 消息模型

![image](https://upload-images.jianshu.io/upload_images/5015984-066ff248d5ff8eed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/401)

消费者（consumer）订阅某个队列。生产者（producer）创建消息，然后发布到队列（queue）中，最后将消息发送到监听的消费者。

- 


## 消息持久化

- 在配置中降queue，exchange和Message都持久化。


## 消息应答机制

- 消费者在收到消息并完成任务后会通知消息队列可以在内存中删除这条消息了。防止消费者挂掉的情况下造成消息丢失。

- 消息应答是默认打开的。
       
## 怎么保证数据不丢失

- 开启消费者自动应答，不消费完就不会删除消息

- 持久化消息队列(queue)，持久化消息(message)，避免宕机的时候数据丢失

## 消息队列应用场景

- 分布式系统中各个模块可以通过异步传输实现服务调用

- 高并发的时候缓解服务器压力(比如：发短信，发邮件，放款，代扣) 


