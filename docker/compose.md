# Compose简介

Compose项目是Docker官方的开源项目，负责实现对Docker容器集群的快速编排。

## 安装

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose

参考 https://docs.docker.com/compose/install/
```

```
#能够编排管理3个容器,php,redis,nginx
version: "3.0"
services: #服务
  nginx:
    image: nginx #指定镜像名称
    container_name: nginx  #容器名称
    ports:  #端口映射
      - "8089:80"
    environment:  #设置环境变量
      password: "123456"
    extra_hosts:
      - "test:192.168.3.3"
    networks:
      redis-network:
          ipv4_address: 192.168.1.8   #设置ip地址
    depends_on:
       - php
    stdin_open: true
    tty:true
    volumes:
      - /usr/docker/test/07/nginx/conf:/conf
    privileged: true  #特殊权限
    working_dir: /conf #工作目录
  php:
    build:
       context: .
       dockerfile:	Dockerfile-alternate
       args:
         test: 1
    #image: php-fpm
    container_name: php-fpm1
    networks:
      redis-network:
        ipv4_address: 192.168.1.7   #设置ip地址
    working_dir: /www #工作目录
    links:
      - redis
    volumes:
      - /usr/docker/test/07/php/www:/www
    stdin_open: true  #打开标准输入
    tty: true  #模拟伪终端
    ports:
      - "9008:9000"
  redis:
    image: redis
    container_name: redis1
    networks:
       redis-network:
         ipv4_address: 192.168.1.6   #设置ip地址
    working_dir: /usr/src/redis #工作目录
    stdin_open: true
    tty: true
    ports:
        - "6399:6379"
    volumes:
      - /usr/docker/redis:/usr/src/redis
#设置网络环境
networks:
  #自定义网络
  nginx:
    driver: bridge
    ipam:
      config:
         - subnet: "172.16.238.0/24"
  #引用外部预先定义好的网段
  redis-network:
   external:
      name: redis-network


```