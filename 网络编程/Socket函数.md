 [TOC]

IO包含网络通信，在了解IO的过程中不可避免的要了解到Socket。

## Socket

Socket是对TCP/IP协议的封装，Socket本身并不是协议，而是一个调用接口（API），通过Socket，我们才能使用TCP/IP协议。实际上，Socket跟TCP/IP协议没有必然的联系。
TCP/IP只是一个协议栈，就像操作系统的运行机制一样，必须要具体实现，同时还要提供对外的操作接口。这个就像操作系统会提供标准的编程接口，比如win32编程接口一样，TCP/IP也要提供可供程序员做网络开发所用的接口，这就是Socket编程接口。

## TCP Socket
```

     主动socket(client)             	  被动socket(server)
------------------------------------------------------------------------
											 s=socket()
											    |
											 bind(s)
											    |
		s=socket()							 listen(s)                       
			|								    |                              
			|							     ns=accept(s)                       
			|								    |                              
		connect(s)	----------------------->    |    
			|								    |                              
		 send(s)	-----------------------> recv(ns)  
			|								    |       
		 recv(s)	<----------------------- send(ns)
			|								    |     
		 close(s)							 close(ns)                          
------------------------------------------------------------------------
```

### TCP 服务器流程

1. socket()：创建套接字         
2. bind()：为套接字绑定地址
3. listen()：监听连接，创建一个队列并将其用于存放来自客户端的连接。 
4. accept()：接受客户的连接。服务器与客户端握手之后，调用accept时，从已经完成连接Socket队列中取出一个socket,  如果没有已经完成连接的socket, 那么accept函数就会阻塞。
5. recv()、send()：发送和接收数据
6. close()：删除套接字

### TCP客户端流程

1. socket()：创建套接字
2. connect(): 连接服务器
5. recv()、send()：发送和接收数据
6. close()：删除套接字

### Socket与TCP三次握手的关系

1. 服务器调用listen()，监听端口，并创建一个存放连接的队列
2. 客户端调用connect()，发起三次握手
3. 服务端三次握手完成之后创建连接，将连接放入队列

## read、write、send、recv、recvfrom、sendto

**为什么不用read()和write()来进行套接字通讯？**

使用send()和recv()让你更好的控制数据传输。recv和send函数提供了和read和write差不多的功能.不过它们提供了第四个参数来控制读写操作。这个参数很少用到，在此不做深究。

recv() 和 send()：面向已连接的TCP/IP类型socket接收或发送数据。

recvfrom() 和 sendto()：面向无连接的UDP数据报。

 send、sento与recv、recvfrom其实只对应两个系统调用：`__sys_sendto`和`__sys_recvfrom`。 

## read() 、write() 


```
#include <unistd.h>
ssize_t read(int fd, void *buf, size_t count);
```

- 参数说明：
  fd: 是文件描述符, 从command line获取数据时，为0
  buf: 为读出数据的缓冲区；
  count: 为每次读取的字节数（是请求读取的字节数，读上来的数据保
  存在缓冲区buf中，同时文件的当前读写位置向后移）

- 返回值：
  成功：返回读出的字节数
  失败：返回-1，并设置errno，如果在调用read
  之前到达文件末尾，则这次read返回0

```
#include <unistd.h>
ssize_t write(int fd, const void *buf, size_t count);
```

- 参数说明：
  fd:是文件描述符（输出到command line，就是1）
  buf:通常是一个字符串，需要写入的字符串
  count：是每次写入的字节数

- 返回值：
  成功：返回写入的字节数
  失败：返回-1并设置errno
  ps： 写常规文件时，write的返回值通常等于请求写的字节
  数count， 而向终端设备或者网络写时则不一定


read函数只是一个通用的读文件设备的接口。是否阻塞需要由设备的属性和设定所决定。一般来说，读字符终端、网络的socket描述字，管道文件等，这些文件的缺省read都是阻塞的方式。如果是读磁盘上的文件，一般不会是阻塞方式的。但使用锁和fcntl设置取消文件O_NOBLOCK状态，也会产生阻塞的read效果。

> errno 用来保存最后的错误代码，它是一个宏，被展开后是一个 int 类型的数据（在单线程程序中可以认为是一个全局变量），并且当前程序拥有读取和写入的权限。
>

##  recvfrom()和sendto()

 sys/socket.h 


```
int recvfrom(SOCKET s,void *buf,int len,unsigned int flags, struct sockaddr *from,int *fromlen);
参数：
s：       标识一个已连接套接口的描述字。
buf：     接收数据缓冲区。
len：     缓冲区长度。
flags：   调用操作方式。
from：   （可选）指针，指向装有源地址的缓冲区。
fromlen：（可选）指针，指向from缓冲区长度值。

若无错误发生，recvfrom()返回读入的字节数。如果连接已中止，返回0。否则的话，返回SOCKET_ERROR错误，应用程序可通过WSAGetLastError()获取相应错误代码。
```
sendto是为UDP而生的。因为这个sendto函数的参数，就要求了这个连接不需要提前建立。sendto函数不关心数据发送者的协议地址，只要知道了对方的地址就行。也就造就了sendto发送之后，你不知道这个数据会不会到达对方，只知道，从本机发送了。

```
int sendto (IN SOCKET s, IN const char FAR * buf, IN int len, IN int flags, IN const struct sockaddr FAR *to, IN int tolen);

参数：
s        套接字
buff     待发送数据的缓冲区
size     缓冲区长度
Flags    调用方式标志位, 一般为0, 改变Flags，将会改变Sendto发送的形式
addr    （可选）指针，指向目的套接字的地址
len addr 所指地址的长度

返回值为整型，如果成功，则返回发送的字节数，失败则返回SOCKET_ERROR。
```

## _sys_recvfrom和__sys_sendto

linux/net/socket.c

recv、recvfrom内部调用__sys_recvfrom

send、sendto内部调用__sys_sendto

##  fcntl阻塞 

阻塞是谁的属性：是设备文件、网络文件的属性（读常规文件无阻塞概念）

所以如果是文件属性的话，是可以改变的(0_NONBLOCK)，可以在文件打开的时候(open)再加上(改变)文件属性

当你第一次调用 socket() 建立套接字描述符的时候，内核就将它设置为阻塞。如果你不想套接字阻塞， 你就要调用函数fcntl()： 

```
#include <unistd.h>
#include <fontl.h>

sockfd = socket(AF_INET, SOCK_STREAM, 0); 
fcntl(sockfd, F_SETFL, O_NONBLOCK); 
```

通过设置套接字为非阻塞，你能够有效地"询问"套接字以获得信息。如果你尝试着从一个非阻塞的套接字读信息并且没有任何数据，它不允许阻塞--它将返回 -1 并将errno 设置为 EWOULDBLOCK。 

但是一般说来，这种询问不是个好主意。如果让程序在忙等状态查询套接字的数据，将浪费大量的 CPU 资源。更好的方法是使用select()去查询是否有数据要读进来。

## FAQ

**c语言中.c和.h文件的困惑**

本质上没有任何区别。 只不过一般：

.h文件是头文件，内含函数声明、宏定义、结构体定义等内容

.c文件是程序文件，内含函数实现，变量定义等内容。

**Socket涉及的头文件？**

sys/socket.h：提供socket函数及数据结构

## 参考资料

 [深入理解TCP协议及其源代码-send和recv背后数据的收发过程 - zhqian - 博客园 (cnblogs.com)](https://www.cnblogs.com/myguaiguai/p/12069485.html) 