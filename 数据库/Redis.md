# Redis

[toc]

### Redis数据结构:

String—字符串（key-value 类型），做缓存用这个

Hash—字典(hashmap) 存储的是键值对；可以利用这个特性存储session

List—列表 基于双线链表；实现消息队列

Set—集合 具有唯一性，可以用来存储用户关注的粉丝

Sorted Set—有序集合 可以做排行榜 可以实现数据持久化

set和list的区别set不能有重复的值

### 适合放在cache中数据：

1 系统的配置数据

2 活跃用户基本信息  不是登陆之后才放缓存    登陆的时候就已经在用缓存里存的信息

3 读远大写的数据

4 基于时间的的统计数据

### 为什么要用redis存储session？
解决分布式和集群环境中不同tomcat的session共享问题

怎么实现？

当用户登陆时，服务器生成一个全局唯一的字符串SESSION:日期:20位随机字符串做为redis中hash数据结构的key名，然后将该标识做为cookie返回给客户端。之后该用户的后续请求都会带上此cookie,我们编写一个filter,其作用为读取请求中的标识，从redis中取出该标识对应的数据，然后放到HttpServletRequest对象中以供后续使用。

### redis持久化
RDB 持久化可以在指定的时间间隔内生成数据集的时间点快照（point-in-time snapshot）。

AOF 持久化记录服务器执行的所有写操作命令，并在服务器启动时，通过重新执行这些命令来还原数据集。

RDB会造成内存飙升，容易丢失数据

redis有六种淘汰策略，默认是不淘汰数据，但是内存不够的时候会报错。

### Redis做异步队列么，你是怎么用的？

一般使用list结构作为队列，rpush生产消息，lpop消费消息。当lpop没有消息的时候，要适当sleep一会再重试。







集群的缺点：

 [精选文章｜Redis集群下mget的性能问题 (qq.com)](https://mp.weixin.qq.com/s/jBUSWAwz3z8-Sz1XEC4Gpg) 