

[TOC]

# Java SPI

SPI（Service Provider Interface）是jdk内置的服务发现机制。

## 原理
META-INF/services目录下放置一个与接口同名的文本文件，
文件的内容为接口的实现类，多个实现类用换行符分隔。
有了实现类的权限定名之后，就通过类加载器将类载入内存，并通过反射创建实例。

## 缺点

1. JDK的SPI会实例化所有实现类，不能按需加载。如果不想用某些实现类，就造成了资源浪费。

2. 获取某个实现类的方式不够灵活，只能通过 Iterator 形式获取，不能根据某个参数来获取对应的实现类。

3. 多个并发多线程使用 ServiceLoader 类的实例是不安全的。

解决办法：使用Dubbo的SPI

## 应用场景
JDBC的驱动
Netty内的SelectorProvider