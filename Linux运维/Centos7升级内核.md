# Centos7升级内核

[TOC]

## 1.首先

- 本文基于centos7环境下升级内核,centos6的升级方法不一样！

- elrepo是CentOS十分有用的稳定的软件源，与其他软件源不一样的是，这个第三方源主要是提供硬件驱动、内核更新方面的支持，如显卡、无线网卡、内核等等，现在已经支持centos7.x+,更新较快。

## 3.查看centos版本

    [root@localhost ~]# cat /etc/centos-release 
    CentOS release 6.4 (Final)

## 4.查看内核版本

    [root@localhost ~]# uname -r
    3.10.0-514.21.1.el7.x86_64


## 5.导入公钥

    rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org 


## 6.安装ELRepo 7

    centos7:
    rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm

## 7.执行安装命令

    yum --enablerepo=elrepo-kernel -y install kernel-lt

## 8.修改配置文件

查看系统的几个内核
    cat /boot/grub2/grub.cfg |grep menuentry

设置启动第几个内核(从0开始) 

    [root@localhost ~]# grub2-set-default 0
    [root@localhost ~]# grub2-editenv list
    saved_entry=0


## 9.重启linux

    reboot