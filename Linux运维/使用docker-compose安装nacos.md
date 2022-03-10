# 使用docker-compose安装nacos

在linux系统中可以通过以下命令安装 docker-compse：

```
yum install -y docker-compose
```

使用如下命令创建部署文件`nacos-standalone-mysql-8.yaml`：

```
vim nacos-standalone-mysql-8.yaml
```

文件内容如下：

```yaml
version: "2"
services:
  nacos:
    image: nacos/nacos-server:2.0.0
    container_name: nacos-standalone-mysql
    environment:
      - PREFER_HOST_MODE=hostname
      - MODE=standalone
      - SPRING_DATASOURCE_PLATFORM=mysql
      - MYSQL_SERVICE_HOST=mysql
      - MYSQL_SERVICE_DB_NAME=nacos_devtest
      - MYSQL_SERVICE_PORT=3306
      - MYSQL_SERVICE_USER=nacos
      - MYSQL_SERVICE_PASSWORD=nacos
    volumes:
      - ./standalone-logs/:/home/nacos/logs
    ports:
      - "8848:8848"
      - "9555:9555"
    depends_on:
      - mysql
    restart: always
  mysql:
    container_name: mysql
    image: nacos/nacos-mysql:8.0.16
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_DATABASE=nacos_devtest
      - MYSQL_USER=nacos
      - MYSQL_PASSWORD=nacos
    volumes:
      - ./mysql:/var/lib/mysql
      - ./mysql:/var/lib/mysql
      - ./mysql:/var/lib/mysql

      - ./docker/mysql/data:/var/lib/mysql
      - ./docker/mysql/conf:/etc/mysql/conf.d
      - ./docker/mysql/logs:/logs

    ports:
      - "3306:3306"
```

使用如下命令启动nacos：

```bash
docker-compose -f nacos-standalone-mysql-8.yaml up -d
```

使用下面的命令可以查看启动日志：

```bash
docker-compose -f nacos-standalone-mysql-8.yaml  logs -f
```

**启动成功后测试**

能否注册服务：

```
curl -X POST "http://127.0.0.1:8848/nacos/v1/ns/instance?serviceName=example&ip=127.0.0.1&port=8080"
```

能否访问管理后台：

http://localhost:8848/nacos

登录用户名：nacos

登录密码：nacos

**遇到的坑：**

- 官方文档并没有提供spring-boot版本与Nacos客户端（nacos-discovery-spring-boot-starter）依赖对应关系，且不支持高版本SpringBoot，导致使用Springboot集成Nacos客户端依赖后经常连不上服务器。

  可以通过降低SpringBoot或者Nacos依赖的版本解决。

```
nacos-discovery-spring-boot-starter
spring-boot-starter-parent
```