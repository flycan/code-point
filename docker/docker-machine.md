

## 安装

```
base=https://github.com/docker/machine/releases/download/v0.16.0 &&
  curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/tmp/docker-machine &&
  sudo mv /tmp/docker-machine /usr/local/bin/docker-machine &&
  chmod +x /usr/local/bin/docker-machine
  ```

## 驱动下载

第三方驱动列表

    https://github.com/docker/docker.github.io/blob/master/machine/AVAILABLE_DRIVER_PLUGINS.md

https://github.com/AliyunContainerService/docker-machine-driver-aliyunecs

```
wget https://docker-machine-drivers.oss-cn-beijing.aliyuncs.com/docker-machine-driver-aliyunecs_linux-amd64.tgz

tar xzvf driver-aliyunecs.tgz -C docker-machine

mv ./bin/docker-machine-driver-aliyunecs.linux-amd64 /usr/local/bin/docker-machine-driver-aliyunecs
```

登录阿里云账号控制台https://home.console.aliyun.com/new#/，选择accesskey

## 创建

docker-machine create -d aliyunecs --aliyunecs-io-optimized=optimized   --aliyunecs-instance-type=ecs.t5-lc1m2.small --aliyunecs-access-key-id=LTAI4FpYWerL5xqaZRpNtect --aliyunecs-access-key-secret=lvanTrYzasPdcKOH52I9hUR4GnqdeR --aliyunecs-region=cn-hangzhou --aliyunecs-ssh-password=Hello123456 manager


### 命令

1、拷贝文件
docker-machine scp -r  host1:/tmp/a  host2:/tmp/b
                               源地址          目标地址
-r  递归目录拷贝    
 -d  传输大文件或更新包含大量文件的目录时，可以使用-d标志，该标志rsync用于传输增量而不是传输所有文件

  --recursive, -r	    递归复制文件(复制目录所需)
   --delta, -d		传输大文件或更新包含大量文件的目录时，可以使用-d标志，该标志rsync用于传输增量而不是传输所有文件
       --quiet, -q


2、查看主机配置信息
docker-machine config worker 


3、挂载共享目录

yum  install fuse-sshfs

docker-machine mount worker:/root/test  test

> 注意

    挂载目录，只能被挂载一次，不能两台远程主机共享一个挂载目录