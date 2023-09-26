# Tomcat 

[toc]

## Tomcat结构

 ![img](https://img-blog.csdn.net/20180109094904328?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzgyNDU1Mzc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center) 

- Tomcat中最顶层的容器是Server，一个Tomcat中只有一个Server，代表着整个服务器。它监听了8005关闭端口，掌握生死大权；

- 一个Server可以包含多个Service，用于具体提供服务；

- 一个Service只有一个Container，但是可以有多个Connectors，这是因为一个服务可以有多个连接，如同时提供Http和Https链接，也可以提供向相同协议不同端口的连接。

- Service主要包含两个部分：Connector和Container；

- Connector用于处理连接相关的事情，并提供Socket与Request和Response相关的转化；
	
	- Connector监听的两个端口
		- 8080 Connector监听的HTTP端口
		- 8009 Connector监听AJP端口，该协议负责和其他的HTTP服务器(如Apache)建立连接，如Tomcat与Apache等集成，前者作Servlet容器，后者处理静态资源。
	
- Container用于封装和管理Servlet，以及具体处理Request请求。

  - Engine：引擎，用来管理多个站点，一个Service最多只能有一个Engine，Engine会根据请求头中的Host把请求分发给对应的Host容器； 
- Host：代表一个站点，也可以叫虚拟主机，通过配置Host就可以添加站点； 
  - Context：代表一个应用程序，对应着平时开发的一套程序，或者一个WEB-INF目录以及下面的web.xml文件； 
- Wrapper：每一Wrapper封装着一个Servlet；

上述的包含关系或者说是父子关系，都可以在tomcat的conf目录下的`server.xml`配置文件中看出

**容器（Container）结构**

 ![img](https://img-blog.csdn.net/20180109110049444?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzgyNDU1Mzc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast) 



## 内存优化

根据服务器物理内存情况配置相关参数优化tomcat性能。当应用程序需要的内存超出堆的最大值时虚拟机就会提示内存溢出，并且导致应用服务崩溃。因此一般建议堆的最大值设置为可用内存的最大值的80%。 Tomcat默认可以使用的内存为128MB，在较大型的应用项目中，这点内存是不够的，需要调大.

Tomcat默认可以使用的内存为128MB,Windows下,在文件/bin/catalina.bat，Unix下，在文件/bin/catalina.sh的前面，增加-Xms、-Xmx、-Xmn等参数值。例如： 

参数详解

```
-server  启用jdk 的 server 版；
-Xms    java虚拟机初始化时的最小内存；
-Xmx    java虚拟机可使用的最大内存；
-Xmn    jvm最小新生代大小
```

32G 内存配置示例：

```
JAVA_OPTS="-server -Xms10g -Xmx10g -Xmn1024m"
```

## 线程优化

tomcat配置文件server.xml中，和连接数相关的参数有：

```
maxThreads： Tomcat使用线程来处理接收的每个请求。这个值表示Tomcat可创建的最大的线程数。默认值150。

acceptCount： 指定当所有可以使用的处理请求的线程数都被使用时，可以放到处理队列中的请求数，超过这个数的请求将不予处理。默认值10。

minSpareThreads： Tomcat初始化时创建的线程数。默认值25。

maxSpareThreads： 一旦创建的线程超过这个值，Tomcat就会关闭不再需要的socket线程。默认值75。

enableLookups： 是否反查域名，默认值为true。为了提高处理能力，应设置为false

connnectionTimeout： 网络连接超时，默认值60000，单位：毫秒。设置为0表示永不超时，这样设置有隐患的。通常可设置为30000毫秒。

maxKeepAliveRequests： 保持请求数量，默认值100。 bufferSize： 输入流缓冲大小，默认值2048 bytes。

compression： 压缩传输，取值on/off/force，默认值off。 其中和最大连接数相关的参数为maxThreads和acceptCount。如果要加大并发连接数，应同时加大这两个参数。
```



32G 内存配置示例：

```
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000" maxThreads="1000" minSpareThreads="60" maxSpareThreads="600"  acceptCount="120"
               redirectPort="8443" URIEncoding="utf-8"/>
```



 [(8条消息) 四张图带你了解Tomcat系统架构_阿雨的博客-CSDN博客_tomcat架构](https://blog.csdn.net/qq_38245537/article/details/79009448) 