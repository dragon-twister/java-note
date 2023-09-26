
# yum更换源

- 备份


    cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
    或者
    mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup

- 下载CentOS-Base.repo 到/etc/yum.repos.d/


    cd   /etc/yum.repos.d/
    wget http://mirrors.163.com/.help/CentOS7-Base-163.repo


- 生成缓存


    yum clean all
    yum makecache

- 更新系统


	yum -y update
