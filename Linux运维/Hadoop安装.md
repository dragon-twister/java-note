# Hadoop安装

- 利用dockerfile生成镜像

    创建Dockerfile文件写入以下信息
	
    ```
    FROM centos
    MAINTAINER lsz
    
    RUN yum -y install openssh-server openssh-clients  net-tools  vim wget
    RUN ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
    RUN cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
    
    RUN mkdir /usr/local/java
    ADD jdk-8u161-linux-x64.tar.gz /usr/local/java/
    RUN ln -s /usr/local/java/jdk1.8.0_161 /usr/local/java/jdk
    
    ADD hadoop-3.1.1.tar.gz /usr/local/
    RUN ln -s /usr/local/hadoop-3.1.1 /usr/local/hadoop
    COPY core-site.xml /usr/local/hadoop/etc/hadoop/
    COPY hdfs-site.xml /usr/local/hadoop/etc/hadoop/
    COPY yarn-site.xml /usr/local/hadoop/etc/hadoop/
    COPY mapred-site.xml /usr/local/hadoop/etc/hadoop/
    
    ENV JAVA_HOME /usr/local/java/jdk
    ENV JRE_HOME ${JAVA_HOME}/jre
    ENV CLASSPATH .:${JAVA_HOME}/lib:${JRE_HOME}/lib
    ENV HADOOP_HOME /usr/local/hadoop
    ENV PATH ${HADOOP_HOME}/bin:${HADOOP_HOME}/sbin:${JAVA_HOME}/bin:$PATH
    
	RUN echo "root:1234" | chpasswd        
	```
	
- 创建hadoopDemo文件夹，放入hadoop-3.1.1.tar.gz，jdk-8u161-linux-x64.tar.gz，core-site.xml，hdfs-site.xml，mapred-site.xml，yarn-site.xml，Dockerfile


- 执行 docker build -t hadoop  C:\Users\Administrator\Desktop\hadoopDemo

- 创建自定义网络类型，并且指定网段

    sudo docker network create --subnet=192.168.0.0/16 staticnet

- 查看网络类型是否增加了一个staticnet

    docker network ls

- 使用新的网络类型创建并启动容器

	```
docker run --privileged -p 5002:22 -p 50070:50070 -p 8088:8088 -it --name hadoop-master  --net staticnet --ip 192.168.0.2 --dns 114.114.114.114 03b74b0b980e /usr/sbin/init
  
  docker run --privileged -p 5003:22 -it --name hadoop-slave1  --net staticnet --ip 192.168.0.3 --dns 114.114.114.114 03b74b0b980e /usr/sbin/init
       
  docker run --privileged -p 5004:22 -it --name hadoop-slave1  --net staticnet --ip 192.168.0.4 --dns 114.114.114.114 03b74b0b980e /usr/sbin/init
  ```

- 配置文件
/usr/local/hadoop/etc/hadoop/hadoop-env.sh，yarn-env.sh增加以下配置

      export JAVA_HOME=/usr/local/java/jdk
	
	start-dfs.sh，stop-dfs.sh添加下列信息：

	```
  HDFS_DATANODE_USER=root
  HADOOP_SECURE_DN_USER=hdfs
  HDFS_NAMENODE_USER=root
  HDFS_SECONDARYNAMENODE_USER=root
  ```
  
    start-yarn.sh，stop-yarn.sh 添加下列信息：
  
  ```
  YARN_RESOURCEMANAGER_USER=root
  HADOOP_SECURE_DN_USER=yarn
  YARN_NODEMANAGER_USER=root
  ```


- tips

    - 修改指定出网ip地址

	  比如将静态IP地址172.168.0.10出去的请求的源地址改成公网IP104.232.36.109(前提是本机存在这个IP地址)，可执行如下命令：
	
	  iptables -t nat -I POSTROUTING -o eth0 -d  0.0.0.0/0 -s 172.18.0.10  -j SNAT --to-source 104.232.36.109

