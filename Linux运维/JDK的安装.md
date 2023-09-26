# JDK的安装

#### 卸载系统自带jdk
    
显示系统安装的jdk:

    rpm -qa | grep java
    
卸载系统自带的openjdk:

    yum remove *openjdk*

#### 安装

    tar -zxvf jdk-8u161-linux-x64.tar.gz 
    mv jdk1.8.0_161/ /usr/local/

#### 设置环境变量

    vim /etc/profile

在文件头部加入:    

    export JAVA_HOME=/usr/local/jdk1.8.0_161
    export JRE_HOME=${JAVA_HOME}/jre  
    export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib  
    export  PATH=${JAVA_HOME}/bin:$PATH

wq保存文件后执行

    source /etc/profile
    java -version