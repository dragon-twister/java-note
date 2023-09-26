# Nginx教程

[toc]

## 安装

> [Nginx的安装](http://note.youdao.com/noteshare?id=342f5bae7b4f8b8f0811f826ee5684e9&sub=FDDA23083F4D458C9F141ED1ED733A59)

## nginx常见功能

- 反向代理
    
Nginx在做反向代理时，提供转发功能。Nginx可以根据不同的正则匹配，采取N不同的转发策略，比如图片文件结尾的走文件服务器，动态页面走web服务器，只要你正则写的没问题。并且Nginx对返回结果进行错误页跳转，异常判断等。如果被分发的服务器存在异常，他可以将请求重新转发给另外一台服务器，然后自动去除异常服务器。

- 负载均衡

Nginx提供的负载均衡策略有2种：内置策略和扩展策略。内置策略为轮询，加权轮询，Ip hash。扩展策略，就天马行空，只有你想不到的没有他做不到的啦。
 ## nginx的五种负载均衡算法
 
1、round-robin

round-robin的意思是循环轮询。Nginx最简单的负载均衡配置如下：

    http {

        upstream app1 {

        server 10.10.10.1;

        server 10.10.10.2;

    }


    server {

        listen 80;

        location / {

            proxy_pass http://app1;

        }

    }

    }

 

upstream app1用来指定一个服务器组，该组的名字是app1，包含两台服务器。在指定服务器组里面包含的服务器时以形式“server ip/domain：port”的形式指定，其中80端口可以忽略。然后在接收到请求时通过“proxy_pass http://app1”把对应的请求转发到组app1上。Nginx默认的负载均衡算法就是循环轮询，如上配置我们采用的就是循环轮询，其会把接收到的请求循环的分发给其包含的（当前可用的）服务器。使用如上配置时，Nginx会把第1个请求给10.10.10.1，把第2个请求给10.10.10.2，第3个请求给10.10.10.1，以此类推。

<!--2、least-connected-->
    
<!--least-connected算法的中文翻译是最少连接，即每次都找连接数最少的服务器来转发请求。例如Nginx负载中有两台服务器，A和B，当Nginx接收到一个请求时，A正在处理的请求数是10，B正在处理的请求数是20，则Nginx会把当前请求交给A来处理。要启用最少连接负载算法只需要在定义服务器组时加上“least_conn”，如：-->

<!--    upstream app1 {-->

<!--        least_conn;-->

<!--        server 10.10.10.1;-->

<!--        server 10.10.10.2;-->

<!--    }-->

 

2、ip-hash

ip-hash可以解决session共享问题
    
ip-hash算法会根据请求的客户端IP地址来决定当前请求应该交给谁。使用ip-hash算法时Nginx会确保来自同一客户端的请求都分发到同一服务器。要使用ip-hash算法时只需要在定义服务器组时加上“ip-hash   ”指令，如：

    upstream app1 {

        ip_hash;

        server 10.10.10.1;

        server 10.10.10.2;

    }

 

3、weighted

weighted算法也就是权重算法，会根据每个服务的权重来分发请求，权重大的请求相对会多分发一点，权重小的会少分发一点。这通常应用于多个服务器的性能不一致时。需要使用权重算法时只需要在定义服务器组时在服务器后面指定参数weight，如：

    upstream app1 {

        server 10.10.10.1 weight=3;

        server 10.10.10.2;

    }
    
## 配置https

实际上，大规模的网站都有很多台Web服务器和应用服务器组成，用户的请求可能是经由 Varnish、HAProxy、Nginx之后才到应用服务器，中间有好几层。而中小规模的典型部署常见的是 Nginx+Tomcat 这种两层配置，而Tomcat 会多于一台，Nginx 作为静态文件处理和负载均衡。

如果Nginx作为前端代理的话，则Tomcat根本不需要自己处理 https，全是Nginx处理的。用户首先和Nginx建立连接，完成SSL握手，而后Nginx 作为代理以 http 协议将请求转给 tomcat 处理，Nginx再把 tomcat 的输出通过SSL 加密发回给用户，这中间是透明的，Tomcat只是在处理 http 请求而已。因此，这种情况下不需要配置 Tomcat 的SSL，只需要配置 Nginx 的SSL 和 Proxy。
  

**在代理模式下，Tomcat 如何识别用户的直接请求（URL、IP、https还是http )？**

在透明代理下，如果不做任何配置Tomcat 认为所有的请求都是 Nginx 发出来的，这样会导致如下的错误结果：

*   request.getScheme() //总是 http，而不是实际的http或https
*   request.isSecure() //总是false（因为总是http）
*   request.getRemoteAddr() //总是 nginx 请求的 IP，而不是用户的IP
*   request.getRequestURL() //总是 nginx 请求的URL 而不是用户实际请求的 URL
*   response.sendRedirect( 相对url ) //总是重定向到 http 上 （因为认为当前是 http 请求）

  

如果程序中把这些当实际用户请求做处理就有问题了。解决方法很简单，只需要分别配置一下 Nginx 和 Tomcat 就好了，而不用改程序。

配置 Nginx 的转发选项：

proxy\_set\_header Host $host;

proxy\_set\_header X-Real-IP $remote_addr;

proxy\_set\_header X-Forwarded-For $proxy\_add\_x\_forwarded\_for;

proxy\_set\_header X-Forwarded-Proto $scheme;

  

配置Tomcat server.xml 的 Engine 模块下配置一个 Value：

<Valve className="org.apache.catalina.valves.RemoteIpValve" remoteIpHeader="X-Forwarded-For"protocolHeader="X-Forwarded-Proto" protocolHeaderHttpsValue="https"/>

  

配置双方的 X-Forwarded-Proto 就是为了正确地识别实际用户发出的协议是 http 还是 https。X-Forwarded-For 是为了获得实际用户的 IP。

这样以上5项测试就都变为正确的结果了，就像用户在直接访问 Tomcat 一样。

  