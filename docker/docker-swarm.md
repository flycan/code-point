# docker-swarm集群

```

端口开放
   2377 集群管理端口
   7946  tcp/udp端口 节点间通讯 
   4789

docker swarm init --advertise-addr xx.xx.xx

docker swarm join-token worker  加入成为工作节点
docker swarm join-token manager 加入成为管理节点

docker swarm join --token SWMTKN-1-5kvbsbxc7n7mzmrplacqg5a94borzrbb8tj9etq3i3c6p235t1-5ccz4ay8q99w115wa0xn42cx3 120.27.250.30:2377

使旧令牌无效并生成新令牌

docker swarm join-token --rotate

> 查看节点

docker node ls

> 节点升降级

将节点升级为manager:  docker node  promote 结点名 
将节点降级为worker：  docker node  demote 结点名

## 部署服务

在manager节点部署nginx服务，服务数量为5个

- 创建服务

    docker service create --replicas 5  -p 8080:80 --name nginx  nginx:latest

- 更新服务

    docker service update  --image  nginx:alpine  nginx

- 删除服务

    docker service rm nginx

- 增加减少服务实例

    docker service scale nginx=0

    3个节点，5个容器。减少到3个容器时，会把多的两个删除
    增加容器也一样，旧的容器尽量不变动

- 查看所有服务
    
    docker service ls
 
- 查看服务的容器状态
    
    docker service ps nginx
 
- 查看服务的详细信息。
    
    docker service inspect nginx

## 创建配置文件

    docker config  create  redis.conf  /redis/redis.conf
    配置文件名称 文件地址

## 使用 docker-compose 部署

    docker stack deploy -c docker-compose.yaml  redis-cluster