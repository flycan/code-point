#### 获取镜像
    docker pull redis:lastest

#### Dockerfile 构建镜像
    docker build -t reids . -f  /docker/dockerfile/RedisDockerfile --no-cache
    
#### 启动容器
    docker  run  -itd  --name redis1 redis
    
#### 查看容器
    docker ps -a 
    
#### 进入容器
    docker  exec  -it  swoft1  bash 

#### 删除容器
    停止所有容器
    docker stop  $(docker ps -a -q)
    docker rm  $(docker ps -a -q) 

#### 重启容器
    1.docker restart redis-master
    2.docker exec nginx nginx - reload

#### 查看镜像
    docker images -a

#### 查看镜像历史
    docker history redis

#### 启动容器
docker run --itd --name redis  --net mynetwork --ip 172.10.0.2 -p 80:8000 -v /www/swoft:/var/www/swoft  redis:lastest

#### 容器网络
    docker network ls
    
#### 自定义网络
    docker  network create  --subnet=192.168.1.0/24  swoftNetwork