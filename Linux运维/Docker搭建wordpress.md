# docker搭建wordpress

- 拉取镜像


	docker pull	wordpress
	docker pull mysql

- 运行


	docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql
	docker run --name wordpress --link mysql:mysql -p 80:80 -d wordpress


- 访问localhost:80,填写mysql用户密码

- 进入容器 


    // id替换为容器id
	docker exec -it id /bin/bash


- 容器内安装vim


    apt-get update 
    apt-get install -y vim

- 修改wordpress配置，避免上传文件报错


    cp /usr/local/etc/php/php.ini-production /usr/local/etc/php/php.ini
    vim /usr/local/etc/php/php.ini
    
    //修改以下值 注意memory_limit>post_max_size>upload_max_filesize;    vim查找命令为:/要查找的字符串，n下一个，N上一个
    upload_max_filesize = 200M    #文件大小限制
    post_max_size = 250M    #post大小限制
    memory_limit = 500M        #内存占用限制


- 退出vim 


	esc
	:wq
	
