# Nginx的安装

[TOC]


## 1:安装工具包 wget、vim和gcc

    yum install -y wget  
    yum install -y vim-enhanced  
    yum install -y make cmake gcc gcc-c++  

## 2:下载nginx安装包

    wget http://nginx.org/download/nginx-1.6.2.tar.gz

## 3:安装依赖包

    yum install -y pcre pcre-devel
    yum install -y zlib zlib-devel
    yum install -y openssl openssl-devel

## 4:解压nginx-1.6.2.tar.gz到/usr/local/目录下

    tar -zxvf nginx-1.6.2.tar.gz -C /usr/local/

## 5:进行configure配置

进入nginx-1.6.2目录然后在执行./configure命令

    ./configure --prefix=/usr/local/nginx --with-http_ssl_module

## 6:编译安装

    make && make install

## 7:启动Nginx

启动命令：

     /usr/local/nginx/sbin/nginx

验证启动：

    ps -ef | grep nginx

关闭命令：
      
    /usr/local/nginx/sbin/nginx -s stop

重新热启动：

    /usr/local/nginx/sbin/nginx -s reload


## 8:配置防火墙,nginx默认的端口是80

    firewall-cmd --zone=public --add-port=80/tcp --permanent

    firewall-cmd --reload

## 9:测试nginx

通过浏览器访问nginx欢迎页，在地址栏输入：[http://192.168.31.241/](http://192.168.31.241/)（80端口可以不用输）或[http://192.168.156.11:80/](http://192.168.156.11:80/)，


## 10:学习nginx配置

在nginx目录下进入conf目录，该目录下有个nginx.conf文件，这是nginx最重要的配置文件

    vim /usr/local/nginx/conf/nginx.conf

**nginx.conf文件的全部内容如下(有注释版):**

     #user  nobody;  
    
    #开启进程数 <=CPU数   
    worker_processes  1;  
    
    #错误日志保存位置  
    #error_log  logs/error.log;  
    #error_log  logs/error.log  notice;  
    #error_log  logs/error.log  info;  
    
    #进程号保存文件  
    #pid        logs/nginx.pid;  
    
    #每个进程最大连接数（最大连接=连接数x进程数）每个worker允许同时产生多少个链接，默认1024  
    events {  
        worker_connections  1024;  
    }  
    
    
    http {  
        #文件扩展名与文件类型映射表  
        include       mime.types;  
        #默认文件类型  
        default_type  application/octet-stream;  
    
        #日志文件输出格式 这个位置相于全局设置  
        #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '  
        #                  '$status $body_bytes_sent "$http_referer" '  
        #                  '"$http_user_agent" "$http_x_forwarded_for"';  
    
        #请求日志保存位置  
        #access_log  logs/access.log  main;  
    
        #打开发送文件  
        sendfile        on;  
        #tcp_nopush     on;  
    
        #keepalive_timeout  0;  
        #连接超时时间  
        keepalive_timeout  65;  
    
        #打开gzip压缩  
        #gzip  on;  
    
        server {  
            #监听端口，默认是80端口  
            listen       80;  
            #监听域名  
            server_name  localhost;  
    
            #charset koi8-r;  
    
            #nginx访问日志放在logs/host.access.log下，并且使用main格式（还可以自定义格式）  
            #access_log  logs/host.access.log  main;  
    
            #如果没有location更明确的匹配访问路径的话，访问请求都会被该location处理。  
            location / {  
                #root指定nginx的根目录为/usr/local/nginx/html  
                root   html;  
                #默认访问文件，欢迎页先去html目录下找index.html，如果找不到再去找index.htm  
                index  index.html index.htm;  
            }  
    
            #error_page  404              /404.html;  
            # redirect server error pages to the static page /50x.html  
            #  
    
            #错误页面及其返回地址，错误码为500、502、503、504都会返回50.html错误页面。  
            error_page   500 502 503 504  /50x.html;  
            #location后面是"="的话，说明是精确匹配  
            location = /50x.html {  
                root   html;  
            }  
    
            # proxy the PHP scripts to Apache listening on 127.0.0.1:80  
            #  
            #location ~ \.php$ {  
            #    proxy_pass   http://127.0.0.1;  
            #}  
    
            # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000  
            #  
            #location ~ \.php$ {  
            #    root           html;  
            #    fastcgi_pass   127.0.0.1:9000;  
            #    fastcgi_index  index.php;  
            #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;  
            #    include        fastcgi_params;  
            #}  
    
            # deny access to .htaccess files, if Apache's document root  
            # concurs with nginx's one  
            #  
            #location ~ /\.ht {  
            #    deny  all;  
            #}  
        }  
    
    
        # another virtual host using mix of IP-, name-, and port-based configuration  
        #  
        #server {  
        #    listen       8000;  
        #    listen       somename:8080;  
        #    server_name  somename  alias  another.alias;  
    
        #    location / {  
        #        root   html;  
        #        index  index.html index.htm;  
        #    }  
        #}  
    
    
        # HTTPS server  
        #  
        #server {  
        #    listen       443 ssl;  
        #    server_name  localhost;  
    
        #    ssl_certificate      cert.pem;  
        #    ssl_certificate_key  cert.key;  
    
        #    ssl_session_cache    shared:SSL:1m;  
        #    ssl_session_timeout  5m;  
    
        #    ssl_ciphers  HIGH:!aNULL:!MD5;  
        #    ssl_prefer_server_ciphers  on;  
    
        #    location / {  
        #        root   html;  
        #        index  index.html index.htm;  
        #    }  
        #}  
    
    }  

配置文件里可以添加多个server，server监听的端口不同，可以根据需要让nginx代理多个端口，当访问某个端口的时候，指定去做某些事情。我这里添加了一个server，这个server监听的端口为1234，server_name我指定为了test.com，也就是域名为test.com，当访问1234端口时会自动导航到/usr/local/nginx/tester/tester111.html页面，如下所示。

    #user  nobody;  
    
    #开启进程数 <=CPU数   
    worker_processes  1;  
    
    #错误日志保存位置  
    #error_log  logs/error.log;  
    #error_log  logs/error.log  notice;  
    #error_log  logs/error.log  info;  
    
    #进程号保存文件  
    #pid        logs/nginx.pid;  
    
    #每个进程最大连接数（最大连接=连接数x进程数）每个worker允许同时产生多少个链接，默认1024  
    events {  
        worker_connections  1024;  
    }  
    
    
    http {  
        #文件扩展名与文件类型映射表  
        include       mime.types;  
        #默认文件类型  
        default_type  application/octet-stream;  
    
        #日志文件输出格式 这个位置相于全局设置  
        #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '  
        #                  '$status $body_bytes_sent "$http_referer" '  
        #                  '"$http_user_agent" "$http_x_forwarded_for"';  
    
        #请求日志保存位置  
        #access_log  logs/access.log  main;  
    
        #打开发送文件  
        sendfile        on;  
        #tcp_nopush     on;  
    
        #keepalive_timeout  0;  
        #连接超时时间  
        keepalive_timeout  65;  
    
        #打开gzip压缩  
        #gzip  on;  
    
        server {  
            #监听端口  
            listen       80;  
            #监听域名  
            server_name  localhost;  
    
            #charset koi8-r;  
    
            #nginx访问日志放在logs/host.access.log下，并且使用main格式（还可以自定义格式）  
            #access_log  logs/host.access.log  main;  
    
            #如果没有location更明确的匹配访问路径的话，访问请求都会被该location处理。  
            location / {  
                #root指定nginx的根目录为/usr/local/nginx/html  
                root   html;  
                #默认访问文件，欢迎页先去html目录下找index.html，如果找不到再去找index.htm  
                index  index.html index.htm;  
            }  
    
            #error_page  404              /404.html;  
            # redirect server error pages to the static page /50x.html  
            #  
    
            #错误页面及其返回地址，错误码为500、502、503、504都会返回50.html错误页面。  
            error_page   500 502 503 504  /50x.html;  
            #location后面是"="的话，说明是精确匹配  
            location = /50x.html {  
                root   html;  
            }  
    
            server {  
                listen 1234;  
                server_name test.com;  
                location / {  
                    #正则表达式匹配uri方式：在/usr/local/nginx/tester下 建立一个tester111.html 然后使用正则匹配  
                    root tester;  
                    index tester111.html;  
                }  
            }  
        }  
    }  
