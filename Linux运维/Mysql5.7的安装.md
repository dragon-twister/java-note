# Mysql5.7的安装

[toc]

### 解压

    tar -zxvf mysql-5.7.21-linux-glibc2.12-x86_64.tar.gz
    
    mv mysql-5.7.21-linux-glibc2.12-x86_64 mysql
    
    mv mysql /usr/local/

### 安装

    cd /usr/local/mysql
    
    mkdir ./data
    
    ./bin/mysqld --initialize  --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data/

此时屏幕输出密码:
    
    [Note] A temporary password is generated for root@localhost: sB)qbuUkl8)F

### 添加用户组和用户

    groupadd mysql
    useradd -r -g mysql mysql
    sudo chown -R root:root /usr/local/mysql
    
    //useradd -r参数表示mysql用户是系统用户，不可用于登录系统。


### 设置开机自启
    

    cp ./support-files/mysql.server /etc/rc.d/init.d/mysqld
    
    chmod +x /etc/rc.d/init.d/mysqld
    
将 mysqld 服务加入到系统服务:
    
    chkconfig --add mysqld
    
检查mysqld服务是否已经生效：

    chkconfig --list mysqld
    
### 修改配置文件

    vim /etc/my.cnf

添加或修改以下内容:

    [client]
    socket=/usr/local/mysql/mysql.sock

    [mysqld] 
    character-set-server=utf8 
    datadir=/usr/local/data 
    basedir=/usr/local/mysql
    socket=/usr/local/mysql/mysql.sock
    
    
### 启动报错 

执行:

    service mysql start

报错:

    Job for mysqld.service failed because the control process exited with error code. See "systemctl status mysqld.service" and "journalctl -xe" for details.

查看报错信息:
    
    systemctl status mysqld.service
    
显示:
    
    mysqld_safe error: log-error set to '/var/log/mariadb/mariadb.log', however file don'...ser 'mysql'

解决办法:

    mkdir /var/log/mariadb 
    touch /var/log/mariadb/mariadb.log 
    chown -R mysql:mysql /var/log/mariadb/

### 登录并修改密码
    
    mysql -u root -p

输入安装时日志输出的密码,进入mysql:

    SET PASSWORD = PASSWORD("****");
    quit;

### 允许远程连接

    mysql -u root -p password
    mysql>use mysql;
    mysql>update user set host ='%'where user ='root' and host ='localhost';
    mysql>flush privileges;