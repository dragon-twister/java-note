[TOC]

# ReentrantLock

## ReentrantLock的实现

AQS(AbstractQuenedSynchronizer)抽象的队列式同步器。

他有三个成员变量包含锁状态（state）、队列、加锁线程。

流程

1.线程先cas操作将status操作为1

2.如果成功就会将加锁线程设置为自己

3.如果失败就表明此对象已经被加锁，判断加锁对象是不是自己，如果是自己就做重入操作，将status累加，不是就进入队列等待

4.释放锁的过程就是将AQS内的state变量的值递减1，如果state值为0，则彻底释放锁，会将“加锁线程”变量也设置为null！

5.释放锁之后，等待队列的队头唤醒线程尝试加锁

## ReentrantLock和Synchronize的区别

 ConcurrentHashMap1.8之前用的是ReentrantLock，jdk1.8的时候又改回了synchronized 

|      | ReenTrantLock | Synchronize |
| ---- | ---- |---- |
| 是否公平 | 公平和非公平锁都支持，默认非公平 | 非公平锁 |
| 底层原理|基于AQS和CAS乐观锁实现| JAVA关键字,JVM层锁,monitorenter和moniterexit指令实现 |
| 是否可手动释放 | 是 | 否 |
| 是否可以重入 | 是 | 是 |



## FAQ

### 主线程如何等待所有子线程执行完毕

thread.join()

CounDownLatch.countDown