# Dockerfile常用命令

```
FROM centos
RUN  groupadd -r redis && useradd -r -g redis redis
RUN  yum update -y && yum install epel-release -y && \
     yum install centos-release-scl-rh -y && \
     yum -y install redis \
RUN  mkdir -p /data && chown -R redis:redis /data

CMD ["redis-server"]
```

> **RUN**

    docker pull redis:lastest

> **CMD容器启动命令**

```text    
    格式：
	shell格式：	CMD	<命令>		
	exec格式：	CMD	["可执行文件",	"参数1",	"参数2"...]	
	参数列表格式：	CMD	["参数1",	"参数2"...]	。
	在指定了ENTRYPOINT指令后，用CMD指定具体的参数
    
    容器中的应用都应该以前台执行，而不是像虚拟机、物理机里面那样， 用	upstart/systemd	去启动后台服务，容器内没有后台服务的概念
```

> **COPY 复制文件**

```
    格式：
	COPY	<源路径>...	<目标路径>		
	COPY	["<源路径1>",...	"<目标路径>"]
	例子：
	COPY /www/redis /redis
	COPY ["/www/redis", "/redis"]
```

> **ADD 更高级的复制文件**

    格式：
	ADD	<源路径>...	<目标路径>		
	ADD	["<源路径1>",...	"<目标路径>"]
	例子：
	ADD /www/redis /redis
	ADD ["/www/redis", "/redis"]

    尽可能的使用COPY
    因此在COPY和ADD指令中选择的时候，可以遵循这样的原则，
    所有的文件复制均使用COPY指令，仅在需要自动解压缩的场合使用ADD

> **ENTRYPOINT	入口点**

ENTRYPOINT的格式和RUN指令格式一样，分为exec格式和shell格式

> **ENV	设置环境变量**

    格式有两种：
    ENV	<key>	<value>		
    ENV	<key1>=<value1>	<key2>=<value2>...

> **ARG	构建参数**

构建参数和		ENV		的效果一样，都是设置环境变量。所不同的是，	ARG		所设置的构建环境的 环境变量，在将来容器运行时是不会存在这些环境变量的。

> **VOLUME	定义匿名卷**

    格式为：
	VOLUME	["<路径1>",	"<路径2>"...]		
    VOLUME	<路径>

> **EXPOSE声明端口**
	
    EXPOSE仅仅是声明 容器打算使用什么端口而已，并不会自动在宿主进行端口映射

> **WORKDIR 工作目录**

> **USER 指定当前用户**
    
WORKDIR	是改变工作目 录，USER则是改变之后层的执行RUN,CMD以及ENTRYPOINT		这类命令的身份。当然，和WORKDIR一样，USER只是帮助你切换到指定用户而已，这个用户必须是事先建立 好的，否则无法切换。
    
    RUN	groupadd -r redis && useradd	-r -g redis redis 
    USER redis 
    RUN	[ "redis-server" ]
   

