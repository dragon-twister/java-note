# Docker的安装

[TOC]

## 1. 前言

本文基于CentOS7 安装Docker CE

Docker CE即社区版:免费

Docker EE即企业版:付费,强调安全

[docker镜像下载地址](https://note.youdao.com/)


## 2. 检查内核版本

    [root@localhost /]#  cat /etc/centos-release 
    CentOS Linux release 7.3.1611 (Core) 
    
    [root@localhost /]# uname -r
    4.4.126-1.el6.elrepo.x86_64

Docker CE 支持 64 位版本 CentOS 7，并且要求内核版本不低于 3.10。

CentOS 7 满足最低内核的要求，但由于内核版本比较低，部分功能（如 overlay2 存储层驱动）无法使用，并且部分功能可能不太稳定。

> [Centos7升级内核](http://www.sobboy.com/view/9)

## 3. 安装 yum-utils

它提供了 yum-config-manager，可用来管理yum源

    yum install -y yum-utils

## 4. 添加yum源

    yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

## 5. 更新索引

    yum makecache fast

## 6. 安装 docker-ce

    yum install docker-ce

## 7. 启动 docker

    systemctl start docker

## 8. 查看是否安装成功

    docker info

## 9. 修复docker pull命令报错

错误信息：
    error pulling image configuration: Get https://dseasb33srnrn.cloudfront.net/

错误原因：
    国内无法连接到 docker hub 

解决办法：
    //把docker源换成daocloud,执行以下三条命令
    
    systemctl stop docker
    
    echo "DOCKER_OPTS=\"\$DOCKER_OPTS --registry-mirror=http://f2d6cb40.m.daocloud.io\"" | sudo tee -a /etc/default/docker
    
    service docker restart

