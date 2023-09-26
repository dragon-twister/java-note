[TOC]
# Dockerfile和docker-compose.yml

基础概念
====

先简单理解 docker 的使用过程，它分为镜像构建与容器启动。

**镜像构建：** 即创建一个镜像，它包含安装运行所需的环境、程序代码等。这个创建过程就是使用 dockerfile 来完成的。

**容器启动：** 容器最终运行起来是通过拉取构建好的镜像，通过一系列运行指令（如端口映射、外部数据挂载、环境变量等）来启动服务的。  

1）针对单个容器，这可以通过 docker run 来运行。

2）而如果涉及多个容器的运行（如服务编排）就可以通过 docker-compose 来实现，它可以轻松的将多个容器作为 service 来运行（当然也可仅运行其中的某个），并且提供了 scale (服务扩容) 的功能。

3）例如，你有一个php镜像，一个mysql镜像，一个nginx镜像。如果没有docker-compose，那么每次启动的时候，你需要敲各个容器的启动参数，环境变量，容器命名，指定不同容器的链接参数等等一系列的操作，相当繁琐。而用了docker-composer之后，你就可以把这些命令一次性写在docker-composer.yml文件中，以后每次启动这一整个环境（含3个容器）的时候，你只要敲一个docker-composer up命令就ok了。

而dockerfile的作用是从无到有的构建镜像。

两个完全不是一码事

**简单总结：**

*   dockerfile: 构建镜像；
    
*   docker run: 启动容器；
    
*   docker-compose: 启动服务；
    

Dockerfile文件详解
==============

什么是Dockerfile?
--------------

Dockerfile是一个包含用于构建镜像的指令的文本文档。

`docker build`命令用于从Dockerfile构建映像。可以在`docker build`命令中使用`-f`标志指向文件系统中任何位置的Dockerfile。

所以构建镜像需要两个步骤：

1. 编写Dockerfile文件
2. 执行 docker build 命令构建镜像

    docker build -f ./Dockerfile


Dockerfile文件说明
---------------

Dockerfile 一般分为四部分：基础镜像信息、维护者信息、镜像操作指令和容器启动时执行指令，’#’ 为 Dockerfile 中的注释。

Docker以从上到下的顺序运行Dockerfile的指令。为了指定基本映像，第一条指令必须是_FROM_。一个声明以`＃`字符开头则被视为注释。可以在Docker文件中使用`RUN`，`CMD`，`FROM`，`EXPOSE`，`ENV`等指令。

**在这里列出了一些常用的指令。**  

| 命令        | 说明                     | 示例                                                         |
| :---------- | :----------------------- | :----------------------------------------------------------- |
| FROM        | 设置基础镜像             | FROM nginx:latest                                            |
| MAINTAINER  | 维护者 (deprecated)      | MAINTAINER ducafecat                                         |
| ENV         | 环境变量                 | ENV localfile /usr/local/nginx                               |
| RUN         | 镜像制作中需要运行的指令 | RUN /bin/bash -c 'source &dollar;HOME/.bashrc; echo $HOME'   |
| LABEL       | 设置镜像标签             | LABEL version="1.0"                                          |
| EXPOSE      | 设置暴露的端口           | EXPOSE 80 443                                                |
| ADD         | 复制文件到镜像中         | ADD ./dist ${foo}/html                                       |
| COPY        | 复制文件到镜像中         | COPY ./dist ${foo}/html                                      |
| ENTRYPOINT  | 容器启动时执行指令       | CMD ["ls"]                                                   |
| CMD         | 容器启动时执行指令       | CMD ["-la"]                                                  |
| VOLUME      | 设置容器的挂载卷         | VOLUME ["/data"]                                             |
| USER        | 指定操作用户             | USER www                                                     |
| WORKDIR     | 设置工作目录             | WORKDIR /path/to/workdir                                     |
| ARG         | 设置参数                 | ARG user=www                                                 |
| ONBUILD     | 镜像被From时触发         | ONBUILD RUN /bin/bash -c 'echo ONBUILD ...'                  |
| STOPSIGNAL  | 停止信号退出             | STOPSIGNAL SIGTERM                                           |
| HEALTHCHECK | 检查容器的健康状况       | HEALTHCHECK --interval=5m --timeout=3s CMD curl -f http://localhost/ |
| SHELL       | 覆盖默认shell            | SHELL ["powershell", "-command"]                             |

**FROM：指定基础镜像，必须为第一个命令**

    格式：
    　　FROM <image>
    　　FROM <image>:<tag>
    　　FROM <image>@<digest>示例：　　
    　　FROM mysql:5.6注：　　tag或digest是可选的，如果不使用这两个值时，会使用latest版本的基础镜像


**MAINTAINER: 维护者信息**

    格式：
        MAINTAINER <name>
    示例：
        MAINTAINER Jasper Xu
        MAINTAINER sorex@163.com
        MAINTAINER Jasper Xu <sorex@163.com>


**RUN：构建镜像时执行的命令**

    RUN用于在镜像容器中执行命令，其有以下两种命令执行方式：
    shell执行
    格式：
        RUN <command>
    exec执行
    格式：
        RUN ["executable", "param1", "param2"]
    示例：
        RUN ["executable", "param1", "param2"]
        RUN apk update
        RUN ["/etc/execfile", "arg1", "arg1"]
    注:
        RUN指令创建的中间镜像会被缓存，并会在下次构建中使用。如果不想使用这些缓存镜像，可以在构建时指定--no-cache参数，如：docker build --no-cache


**ADD：将本地文件添加到容器中，tar类型文件会自动解压(网络压缩资源不会被解压)，可以访问网络资源，类似wget**

    格式：
        ADD <src>... <dest>
        ADD ["<src>",... "<dest>"] 用于支持包含空格的路径
    示例：
        ADD hom* /mydir/          # 添加所有以"hom"开头的文件
        ADD hom?.txt /mydir/      # ? 替代一个单字符,例如："home.txt"
        ADD test relativeDir/     # 添加 "test" 到 `WORKDIR`/relativeDir/
        ADD test /absoluteDir/    # 添加 "test" 到 /absoluteDir/


**COPY：功能类似ADD，但是是不会自动解压文件，也不能访问网络资源**

**CMD：构建容器后调用，也就是在容器启动时才进行调用。**

    格式：
        CMD ["executable","param1","param2"] (执行可执行文件，优先)
        CMD ["param1","param2"] (设置了ENTRYPOINT，则直接调用ENTRYPOINT添加参数)
        CMD command param1 param2 (执行shell内部命令)
    示例：
        CMD echo "This is a test." | wc -
        CMD ["/usr/bin/wc","--help"]注： 　　CMD不同于RUN，CMD用于指定在容器启动时所要执行的命令，而RUN用于指定镜像构建时所要执行的命令。


**ENTRYPOINT：配置容器，使其可执行化。配合CMD可省去"application"，只使用参数。**

    格式：
        ENTRYPOINT ["executable", "param1", "param2"] (可执行文件, 优先)
        ENTRYPOINT command param1 param2 (shell内部命令)
    示例：
        FROM ubuntu
        ENTRYPOINT ["top", "-b"]
        CMD ["-c"]注：　　　ENTRYPOINT与CMD非常类似，不同的是通过docker run执行的命令不会覆盖ENTRYPOINT，而docker run命令中指定的任何参数，都会被当做参数再次传递给ENTRYPOINT。Dockerfile中只允许有一个ENTRYPOINT命令，多指定时会覆盖前面的设置，而只执行最后的ENTRYPOINT指令。


**LABEL：用于为镜像添加元数据**

    格式：
        LABEL <key>=<value> <key>=<value> <key>=<value> ...
    示例：
    　　LABEL version="1.0" description="这是一个Web服务器" by="IT笔录"
    注：
    　　使用LABEL指定元数据时，一条LABEL指定可以指定一或多条元数据，指定多条元数据时不同元数据之间通过空格分隔。推荐将所有的元数据通过一条LABEL指令指定，以免生成过多的中间镜像。


**ENV：设置环境变量**

    格式：
        ENV <key> <value>  #<key>之后的所有内容均会被视为其<value>的组成部分，因此，一次只能设置一个变量
        ENV <key>=<value> ...  #可以设置多个变量，每个变量为一个"<key>=<value>"的键值对，如果<key>中包含空格，可以使用\来进行转义，也可以通过""来进行标示；另外，反斜线也可以用于续行
    示例：
        ENV myName John Doe
        ENV myDog Rex The Dog
        ENV myCat=fluffy


**EXPOSE：指定于外界交互的端口**

    格式：
        EXPOSE <port> [<port>...]
    示例：
        EXPOSE 80 443
        EXPOSE 8080    EXPOSE 11211/tcp 11211/udp注：　　EXPOSE并不会让容器的端口访问到主机。要使其可访问，需要在docker run运行容器时通过-p来发布这些端口，或通过-P参数来发布EXPOSE导出的所有端口


**VOLUME：用于指定持久化目录**

    格式：
        VOLUME ["/path/to/dir"]
    示例：
        VOLUME ["/data"]
        VOLUME ["/var/www", "/var/log/apache2", "/etc/apache2"注：　　一个卷可以存在于一个或多个容器的指定目录，该目录可以绕过联合文件系统，并具有以下功能：
    1 卷可以容器间共享和重用
    2 容器并不一定要和其它容器共享卷
    3 修改卷后会立即生效
    4 对卷的修改不会对镜像产生影响
    5 卷会一直存在，直到没有任何容器在使用它

**WORKDIR：工作目录，类似于cd命令**

    格式：
        WORKDIR /path/to/workdir
    示例：
        WORKDIR /a  (这时工作目录为/a)
        WORKDIR b  (这时工作目录为/a/b)
        WORKDIR c  (这时工作目录为/a/b/c)注：　　通过WORKDIR设置工作目录后，Dockerfile中其后的命令RUN、CMD、ENTRYPOINT、ADD、COPY等命令都会在该目录下执行。在使用docker run运行容器时，可以通过-w参数覆盖构建时所设置的工作目录。


**USER: 指定运行容器时的用户名或 UID，后续的 RUN 也会使用指定用户。使用USER指定用户时，可以使用用户名、UID或GID，或是两者的组合。当服务不需要管理员权限时，可以通过该命令指定运行用户。并且可以在之前创建所需要的用户**

     格式:
    　　USER user
    　　USER user:group
    　　USER uid
    　　USER uid:gid
    　　USER user:gid
    　　USER uid:group
    
     示例：
    　　USER www
     注：
    　　使用USER指定用户后，Dockerfile中其后的命令RUN、CMD、ENTRYPOINT都将使用该用户。镜像构建完成后，通过`docker run`运行容器时，可以通过-u参数来覆盖所指定的用户。


**ARG：用于指定传递给构建运行时的变量**

    格式：
        ARG <name>[=<default value>]
    示例：
        ARG site
        ARG build_user=www


**ONBUILD：用于设置镜像触发器**

    格式：　　ONBUILD [INSTRUCTION]
    示例：
    　　ONBUILD ADD . /app/src
    　　ONBUILD RUN /usr/local/bin/python-build --dir /app/src
    注：　　当所构建的镜像被用做其它镜像的基础镜像，该镜像中的触发器将会被钥触发

**以下是一个小例子：**

    # Base images 基础镜像
    FROM centos
    
    #MAINTAINER 维护者信息
    MAINTAINER test 
    
    #ENV 设置环境变量
    ENV PATH /usr/local/nginx/sbin:$PATH
    
    #ADD  文件放在当前目录下，拷过去会自动解压
    ADD nginx-1.8.0.tar.gz /usr/local/  
    ADD epel-release-latest-7.noarch.rpm /usr/local/  
    
    #RUN 执行以下命令 
    RUN rpm -ivh /usr/local/epel-release-latest-7.noarch.rpm
    RUN yum install -y wget lftp gcc gcc-c++ make openssl-devel pcre-devel pcre && yum clean all
    RUN useradd -s /sbin/nologin -M www
    
    #WORKDIR 相当于cd
    WORKDIR /usr/local/nginx-1.8.0 
    
    RUN ./configure --prefix=/usr/local/nginx --user=www --group=www --with-http_ssl_module --with-pcre && make && make install
    
    RUN echo "daemon off;" >> /etc/nginx.conf
    
    #EXPOSE 映射端口
    EXPOSE 80
    
    #CMD 运行以下命令
    CMD ["nginx"]

**解决dockerfile时区问题，可以加上命令：**

    RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
    RUN echo 'Asia/Shanghai' >/etc/timezone

docker-compose文件及命令
===================

docker-compose文件
----------------

先来看一份 docker-compose.yml 文件

```
version: "3"
services:

   db:
     image: mysql:8.0
     command:
      - --default_authentication_plugin=mysql_native_password
      - --character-set-server=utf8mb4
      - --collation-server=utf8mb4_unicode_ci     
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
volumes:
  db_data:

```

 运行 `docker-compose up -d` Compose 就会拉取镜像再创建我们所需要的镜像，然后启动 `wordpress` 和数据库容器。 接着浏览器访问 `127.0.0.1:8000` 端口就能看到 `WordPress` 安装界面了。 

docker-compose常用命令：
-------------------

    docker-compose up -d nginx                     构建建启动nignx容器
    
    docker-compose exec nginx bash            登录到nginx容器中
    
    docker-compose down                              删除所有nginx容器,镜像
    
    docker-compose ps                                   显示所有容器
    
    docker-compose restart nginx                   重新启动nginx容器
    
    docker-compose run --no-deps --rm php-fpm php -v  在php-fpm中不启动关联容器，并容器执行php -v 执行完成后删除容器
    
    docker-compose build nginx                     构建镜像 。        
    
    docker-compose build --no-cache nginx   不带缓存的构建。
    
    docker-compose logs  nginx                     查看nginx的日志 
    
    docker-compose logs -f nginx                   查看nginx的实时日志
    
    docker-compose config  -q                   验证（docker-compose.yml）文件配置，当配置正确时，不输出任何内容，当文件配置错误，输出错误信息。 
    
    docker-compose events --json nginx       以json的形式输出nginx的docker日志
    
    docker-compose pause nginx                 暂停nignx容器
    
    docker-compose unpause nginx             恢复ningx容器
    
    docker-compose rm nginx                       删除容器（删除前必须关闭容器）
    
    docker-compose stop nginx                    停止nignx容器
    
    docker-compose start nginx                    启动nignx容器

> [实战 WordPress - Docker —— 从入门到实践 (gitbook.io)](https://yeasy.gitbook.io/docker_practice/compose/wordpress) 

  

