# shell 命令

## vim

dd 剪切
yy 复制
p 粘贴
u 回退
/ 搜索
n 查看下一个关键字
N 查看上一个关键字

## linux：
nohup & ：退出的时候不退出程序
uname -a ：查看系统信息
cat /etc/issue：查看系统信息（容器内） 。 cat /proc/version 或 uname -a 查到的是宿主机的系统。
nohup java -jar refech-tax.jar --spring.datasource.url=jdbc:oracle:thin:@192.168.81.77:1521:NINVOICE --spring.datasource.username=jrwz2_zx  --spring.datasource.password=jrwz2_zx  --server.port=8081  --logging.file=logs/refech.log   > /dev/null 2>&1 &


## Docker:
docker ps：正在运行的容器
docker ps -a：所有容器
docker rm：删除容器
docker rmi：删除镜像
docker run --name refech-tax1 -p 8081:8080 -it 9d2740120aa7  /bin/bash : 运行镜像
docker exec -it cb5121f88849 /bin/bash ：进入容器
docker save -o C:\Users\Administrator\Desktop\111\zxxt-shandong.tar 9de2014f4654   导出镜像到本地
docker load -i C:\Users\Administrator\Desktop\111\zxxt-shandong.tar  导入镜像
docker cp C:\Users\Administrator\Desktop\centos7-jdk8\refech-tax.jar 0c0c304d8b4f:/usr/local
docker build -t centos7-jdk8:1.0.0 . :  -t name:tag dockerfile的目录
docker build -t centos7-jdk8:1.0.0 -f . C:  :  -t name:tag  -f dockerfile的目录 MV,COPY,ADD使用的相对位置  (解决Dockerfile文件与需要拷贝的文件不在同一个目录下的问)
Ctrl+P+Q ：退出容器操作界面
apt-get update、apt-get install vim : 无法使用vi时


## git

sudo apt-get install git

git config --global user.name "your name"        //设置用户名
git config --global user.email "your email"        //设置电子邮箱
git config user.name                                   //查看用户名
git config user.email                                  //查看电子邮箱
git config --list                                      //查看所有用户信息

注意git config命令的--global参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。

## CMD:
telnet 192.192.193.211 22 : 测试端口
dir:查看当前目录的文件


## 创建用户

useradd testuser  创建用户testuser
passwd testuser  给已创建的用户testuser设置密码

说明：新创建的用户会在/home下创建一个用户目录testuser

usermod --help  修改用户这个命令的相关参数
userdel testuser  删除用户testuser
rm -rf testuser  删除用户testuser所在目录

### 常见端口

smtp 25 
ftp 21 
ssh 22
http 443 
mysql 3306
redis 6379


## sudo chmod 777

Linux的文件调用权限分为三级 : 文件拥有者、群组、其他。
利用 chmod 可以控制文件如何被他人所调用。
三位数字分别代表 user、group、others 的权限，
可读(r)、可写(w)、可执行(x)的权限分别用数字 4、2、1 表示，数字7是权限 4、2、1 的和
777 即代表 user、group、others均具有可读(r)、可写(w)、可执行(x)的权限，为最高权限。

## netstat和lsof

netstat： 显示各种网络详细信息。 如TCP UDP 路由器等相关信息

lsof: 先要理解 在LInux环境下， 任何事物都以文件的形式存在，通过文件不仅仅可以访问常规数据，还可以访问网络连接和硬件。所以如传输控制协议 (TCP) 和用户数据报协议 (UDP) 套接字等，系统在后台都为该应用程序分配了一个文件描述符，无论这个文件的本质如何，该文件描述符为应用程序与基础操作系统之间的交互提供了通用接口。因为应用程序打开文件的描述符列表提供了大量关于这个应用程序本身的信息，因此通过lsof工具能够查看这个列表对系统监测以及排错将是很有帮助的。
lsof（list open files）是一个列出当前系统打开文件的工具

  

- 详细描述

netstat

netstat 在不指定参数的时候不显示PID
netstat -a 显示所有socket，包括正在监听的。(默认不显示LISTEN相关)  
netstat -at列出所有TCP链接
netstat -au 列出所所有UDP链接
netstat -l 列出所有监听状态的SOCKET
netstat -p 显示PID， 可以和别的参数合并使用 netstat -pt

lsof
lsof filename 显示打开指定文件的所有进程  
lsof -u username 显示所属user进程打开的文件  
lsof -i 用以显示符合条件的进程情况  

综合：
根据端口port查进程
   netstat -nap | grep port
      lsof  -i:port  
  
根据进程pid查端口  

 netstat -nap | grep pid
 lsof -i | grep pid
