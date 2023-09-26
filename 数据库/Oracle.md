# Oracle

[TOC]

## 基础

**可视化工具:**

plsql developer 需要安装oracle客户端和修改配置文件

**数据库:**

数据库存储在硬盘的一系列文件,Oracle安装后可以创建多个数据库.

**实例:**

实例是一个正在进行时的概念，每当启动一个数据库时，ORACLE会依照初始化参数文件spfile的记录分配内存，启动进程，查找相关文件，而当数据库关闭后，实例的寿命也就终止了，再启动就是一个新的实例，可以简单理解为实例就是内存和进程的集合，与物理文件没有关联，你完全可以删除实例，然后用oradmin来创建一个新实例，然后启动数据库，只要物理文件没有问题。多个实例可对应一个数据库，它们共同操作同一数据文件。但你要说多个数据库，应该是指要创建不同的物理文件，而一个实例不能管理多个数据库。
Oracle一个实例不能管理多个物理文件

**一般不用创建数据库,创建用户就可以了**

表空间和用户:

表空间可以用来限制用户使用磁盘空间的大小

表空间也是个逻辑概念，本质上是一个或者多个数据文件的集合。

一个用户可以有很多张表,但是一张表只能有一个用户

一张表可以放到A仓库或者B仓库,但是不能同时放入A和B

表空间不属于任何商家.

表空间：创建表空间会在物理磁盘上建立一个数据文件，作为数据库对象（用户、表、存储过程等等）的物理存储空间；

用户：创建用户必须为其指定表空间，如果没有显性指定默认表空间，即users表空间；创建用户后，可以在用户上，创建表、存储过程等等其他数据库对象；

步骤:建表空间，建用户，设置用户的默认表空间，在用户下建表；

**临时表空间和默认表空间的区别:**
Oracle临时表空间主要用来做查询和存放一些缓冲区数据。临时表空间，可自动释放；而表空间中存储表数据、函数、过程、序列等。是随数据库永久存在的。

**sql:**

    创建用户:
    必须的要有创建用户的权限
    create user 用户名 identified by 密码 
    default tablespace XXX
    
    授权:
    新创建的用户是没有权限的，需要授权. 
    grant 权限 to 用户; 
    grant connect,resource to zhou; 
    
    撤销权限:
     revoke connect, resource from 用户名
    
    切换用户: 
    connect user/passwrod； 
    
    查看所有表空间:
    select * from sys.dba_tablespaces;
    
    查看用户信息:
    select * from user_users;
    
    查看所有表空间的数据文件位置:
    select t1.name,t2.name  from v$tablespace t1,v$datafile t2 where t1.ts# = t2.ts#;

## Mysql和Oracle的区别

**用法上:**

 1.分页问题：

 Oracle没有offet,limit，在mysql中我们用它们来控制分页了。oracle要分页的话，要换成rownum。

2.自增问题

oracle建表时，没有auto_increment，所有要想让表的一个字段自增，要自己添加序列，插入时，把序列的值，插入进去。

3.对空值的判断

name != ""这样在mysql下不会报错的，但是oracle下会报错。在oracle下的要换成name is not null

4.oracle下对单引号，双引号要求的很死，一般不准用双引号，用了会报

    ERROR at line 1:
    ORA-00904: "t": invalid identifier

而MySQL要求就没有那么严格了，单引号，双引号都可以。

5. 索引：

mysql的行锁基于索引，没有命中索引的话就是用表锁。
oracle的行锁不依赖索引，所以并发性比较好。

## 不喜欢oracle的几点

- maven不能下载oracle的jar包
- 主键不能自动递增

## FAQ

### 解决死锁

    select object_id,session_id,locked_mode from v$locked_object;
     
    alter system  kill session  '56, 29891'; 
    
    select t2.username,t2.sid,t2.serial#,t2.logon_time from v$locked_object t1,v$session t2 where t1.session_id=t2.sid order by t2.logon_time;