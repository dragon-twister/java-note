# Linux下iptables防火墙配置详解

[TOC]

## 1\. iptables命令及参数介绍

**iptables常用命令：**

*   iptables -A 将一个规则添加到链末尾
*   iptables -D 将指定的链中删除规则
*   iptables -F 将指定的链中删除所有规则
*   iptables -I 将在指定链的指定编号位置插入一个规则
*   iptables -L 列出指定链中所有规则
*   iptables -t nat -L 列出所有NAT链中所有规则
*   iptables -N 建立用户定义链
*   iptables -X 删除用户定义链
*   iptables -P 修改链的默认设置，如将iptables -P INPUT DROP (将INPUT链设置为DROP)

**常见设置参数介绍：**

*   --dport 指定目标TCP/IP端口 如 –dport 80
*   --sport 指定源TCP/IP端口 如 –sport 80
*   -p tcp 指定协议为tcp
*   -p icmp 指定协议为ICMP
*   -p udp 指定协议为UDP
*   -j DROP 拒绝
*   -j ACCEPT 允许
*   -j REJECT 拒绝并向发出消息的计算机发一个消息
*   -j LOG 在/var/log/messages中登记分组匹配的记录
*   -m mac –mac 绑定MAC地址
*   -m limit –limit 1/s 1/m 设置时间策列
*   -s 10.10.0.0或10.10.0.0/16 指定源地址或地址段
*   -d 10.10.0.0或10.10.0.0/16 指定目标地址或地址段
*   -s ! 10.10.0.0 指定源地址以外的

**iptables配置文件**

> 配置文件位置： /etc/sysconfig/iptables

**iptables服务命令**

    -- 启动服务
    # /etc/init.d/iptables start 
    # service iptables start

    -- 停止服务
    # /etc/init.d/iptables stop
    # service iptables stop

    -- 重启服务
    # /etc/init.d/iptables restart
    # service iptables restart

    -- 保存设置
    # /etc/init.d/iptables save
    # service iptables save

## 2\. 配置Filter表防火墙

1.  查看iptables的配置信息

        # iptables -L -n

2.  清除原有防火墙规则

    *   清除预设表filter中的所有规则链的规则

        # iptables -F

    *   清除预设表filter中使用者自定链中的规则

        # iptables -X

    *   保存防火墙设置

        # /etc/init.d/iptables save
        或
        # service iptables save

3.  设定预设规则

        -- 请求接入包丢弃
        [root@home ~]# iptables -p INPUT DROP
        -- 接受响应数据包
        [root@home ~]# iptables -p OUTPUT ACCEPT
        -- 转发数据包丢弃 
        [root@home ~]# iptables -p FORWARD DROP

4.  添加防火墙规则

    首先添加INPUT链,INPUT链的默认规则是DROP,所以我们就写需要ACCETP(通过)的链。

    *   **开启SSH服务端口**

        [root@tp ~]# iptables -A INPUT -p tcp --dport 22 -j ACCEPT
        [root@tp ~]# iptables -A OUTPUT -p tcp --sport 22 -j ACCEPT

    > 注:如果在预设设置把OUTPUT设置成DROP策略的话，就需要设置OUTPUT规则，否则无法进行SSH连接。

    *   **开启Web服务端口**

        [root@tp ~]# iptables -A OUTPUT -p tcp --sport 80 -j ACCEPT
        [root@tp ~]# iptables -A INPUT -p tcp --dport 80 -j ACCEPT

    *   **开启邮件服务的25、110端口**

        [root@tp ~]# iptables -A INPUT -p tcp --dport 110 -j ACCEPT
        [root@tp ~]# iptables -A INPUT -p tcp --dport 25 -j ACCEPT

    *   **开启FTP服务的21端口**

        [root@tp ~]# iptables -A INPUT -p tcp --dport 21 -j ACCEPT
        [root@tp ~]# iptables -A INPUT -p tcp --dport 20 -j ACCEPT

    *   **开启DNS服务的53端口**

        [root@tp ~]# iptables -A INPUT -p tcp --dport 53 -j ACCEPT

    *   **设置icmp服务**

        [root@tp ~]# iptables -A OUTPUT -p icmp -j ACCEPT (OUTPUT设置成DROP的话)
        [root@tp ~]# iptables -A INPUT -p icmp -j ACCEPT    (INPUT设置成DROP的话)

    *   **允许loopback**

    > 不然会导致DNS无法正常关闭等问题

        [root@tp ~]# IPTABLES -A INPUT -i lo -p all -j ACCEPT 
        (如果是INPUT DROP)
        [root@tp ~]# IPTABLES -A OUTPUT -o lo -p all -j ACCEPT
        (如果是OUTPUT DROP)

    *   **减少不安全的端口连接**

        [root@tp ~]# iptables -A OUTPUT -p tcp --sport 31337 -j DROP
        [root@tp ~]# iptables -A OUTPUT -p tcp --dport 31337 -j DROP

    > 说明：有些特洛伊木马会扫描端口31337到31340(即黑客语言中的 elite 端口)上的服务。既然合法服务都不使用这些非标准端口来通信,阻塞这些端口能够有效地减少你的网络上可能被感染的机器和它们的远程主服务器进行独立通信的机会。此外，其他端口也一样,像:31335、27444、27665、20034 NetBus、9704、137-139（smb）,2049(NFS)端口也应被禁止。

    *   **只允许某台主机或某个网段进行SSH连接**

    只允许192.168.0.3的机器进行SSH连接

        [root@tp ~]# iptables -A INPUT -s 192.168.0.3 -p tcp --dport 22 -j ACCEPT

    如果允许或限制一段IP地址可用`192.168.0.0/24`表示192.168.0.1-255端的所有IP, 24表示子网掩码数。

        [root@tp ~]# iptables -A INPUT -s 192.168.0.0/24 -p tcp --dport 22 -j ACCEPT

    > 注意：指定某个主机或者某个网段进行SSH连接，需要在iptables配置文件中的`-A INPUT -p tcp -m tcp --dport 22 -j ACCEPT`  
    > 删除，因为它表示所有地址都可以登陆.

    如果只允许除了192.168.0.3的主机外都能进行SSH连接

        [root@tp ~]# iptables -A INPUT -s ! 192.168.0.3 -p tcp --dport 22 -j ACCEPT

    *   **开启转发功能**

    在做NAT网络配置时,FORWARD默认规则是DROP时,必须开启数据包转发功能

        [root@tp ~]# iptables -A FORWARD -i eth0 -o eth1 -m state --state RELATED,ESTABLISHED -j ACCEPT
        [root@tp ~]# iptables -A FORWARD -i eth1 -o eh0 -j ACCEPT

    *   **丢弃坏的TCP包**

        [root@tp ~]#iptables -A FORWARD -p TCP ! --syn -m state --state NEW -j DROP

    *   **处理IP碎片数量，防止DDOS攻击，允许每秒100个**

        [root@tp ~]#iptables -A FORWARD -f -m limit --limit 100/s --limit-burst 100 -j ACCEPT

    *   **设置ICMP包过滤, 允许每秒1个包, 限制触发条件是10个包**

        [root@tp ~]#iptables -A FORWARD -p icmp -m limit --limit 1/s --limit-burst 10 -j ACCEPT

    *   **DROP非法连接**

        [root@tp ~]# iptables -A INPUT   -m state --state INVALID -j DROP
        [root@tp ~]# iptables -A OUTPUT  -m state --state INVALID -j DROP
        [root@tp ~]# iptables -A FORWARD -m state --state INVALID -j DROP

    *   **允许所有已经建立的和相关的连接**

        [root@tp ~]# iptables-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
        [root@tp ~]# iptables-A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
        [root@tp ~]# /etc/rc.d/init.d/iptables save

## 3\. 配置NAT表防火墙

1.  查看本机关于NAT的设置情况

        [root@tp rc.d]# iptables -t nat -L

2.  清除NAT规则

        [root@tp ~]# iptables -F -t nat
        [root@tp ~]# iptables -X -t nat
        [root@tp ~]# iptables -Z -t nat

3.  添加规则

    添加基本的NAT地址转换，添加规则时，我们只添加DROP链，因为默认链全是ACCEPT。

    *   **防止外网用内网IP欺骗**

        [root@tp sysconfig]# iptables -t nat -A PREROUTING -i eth0 -s 10.0.0.0/8 -j DROP
        [root@tp sysconfig]# iptables -t nat -A PREROUTING -i eth0 -s 172.16.0.0/12 -j DROP
        [root@tp sysconfig]# iptables -t nat -A PREROUTING -i eth0 -s 192.168.0.0/16 -j DROP

    *   **禁止与211.101.46.253的所有连接**

        [root@tp ~]# iptables -t nat -A PREROUTING -d 211.101.46.253 -j DROP

    *   **禁用FTP(21)端口**

        [root@tp ~]# iptables -t nat -A PREROUTING -p tcp --dport 21 -j DROP

    只禁用211.101.46.253地址的FTP连接,其他连接可以进行。

        [root@tp ~]# iptables -t nat -A PREROUTING -p tcp --dport 21 -d 211.101.46.253 -j DROP
