> 什么是nginx?

    Nginx 是一款自由的、开源的、高性能的HTTP服务器和反向代理服务器；同时也是一个IMAP、POP3、SMTP代理服务器； Nginx 可以作为一个HTTP服务器进 行网站的发布处理，另外 Nginx 可以作为反向代理进行负载均衡的实现。


> Nginx应用场景

    1、静态资源服务（通过本地文件系统提供服务）
    2、缓存、负载均衡服务器
    3、API服务（OpenResty）

> Nginx的优势

- 快
- 高可扩展 模块化
- 高可靠性
- 低内存消耗
- 支持高并发连接
- 热部署

> Nginx网络模型

常见网络模型

1.单进程阻塞

```php
    $socket = stream_socket_server("tcp://0.0.0.0:9500",$errno, $errstr, STREAM_SERVER_BIND| STREAM_SERVER_LISTEN);
    while(true) {
        //阻塞获取客户端的请求
        $clientSocket=stream_socket_accept($socket);
        $message = fread($clientSocket, 65535);
        print_r($message);
        $resonse = "HTTP/1.1 200 OK\r\n";
        $resonse .= "Content-Type: text/html;charset=UTF-8\r\n";
        $resonse .= "Connection: keep-alive\r\n";
        $resonse .= "Server: php socket server\r\n";
        $resonse .= "Content-length: ".strlen($message)."\r\n\r\n";
        $resonse .= $message;
        fwrite($clientSocket, $resonse);
    }
```

2.多进程阻塞

3.单进程IO复用方式 I/O复用使得程序能同时监听多个文件描述符

    1、保存所有的socket,通过select模型，监听socket描述符的可读事件
    2、Select会在内核空间监听一旦发现socket可读，会从内核空间传递至用户空间，在用户空间通过逻辑判断是服务端socket可读，还是客户端 的socket可读
    3、如果是服务端的socket可读，说明有新的客户端建立，将socket保留到监听数组当中
    4、如果是客户端的socket可读，说明当前已经可以去读取客户端发送过来的内容了，通过fread读取socket内容，然后fwrite响应给客户端。

nginx的基本架构

多进程的master-worker IO复用方式

    1.Nginx启动后，会产生一个主进程，主进程执行一系列的工作后会产生一个或者多个工作进程
    2.在客户端请求动态站点的过程中，Nginx服务器还涉及和后端服务器的通信。Nginx将接收到的Web请求通过代理转发到后端服务器，由后端服务器进行 数据处理和组织；
    3.Nginx为了提高对请求的响应效率，降低网络压力，采用了缓存机制，将历史应答数据缓存到本地。保障对缓存文件的快速访问

master进程主要用来管理 worker 进程

    （1）接收来自外界的信号。 
    （2）处理配置文件读取。 
    （3）创建，绑定和关闭套接字 
    （4）启动，终止和维护配置的工作(worker)进程数 
    （5）当woker进程退出后（异常情况下），会自动重新启动新的woker进程。

>  nginx常用命令

    1、查看Nginx的版本号：nginx  -V 
    2、停止 nginx -s stop 
    3、退出 nginx -s quit 
    4、重启加载配置 nginx -s reload 
    5、配置文件启动 nginx  -c </path/to/config> 为 Nginx 指定一个配置文件，来代替缺省的 
    6、nginx  -t 不运行，而仅仅测试配置文件。nginx 将检查配置文件的语法的正确性，并尝试打开配置文件中所引用到的文件

> nginx控制信号

    Kill -QUIT  PID（nginx 主进程号） 停止命令，等所有请求结束后关闭服务

    kill -HUP  PID 重新载入配置

    kill -TERM, INT PID 快速关闭
    kill -USR1 PID  重新打开日志文件
    kill -USR2 平滑升级可执行程序

> nginx安装

```
cd /usr/local/src
wget http://nginx.org/download/nginx-1.17.5.tar.gz
tar -zxvf nginx-1.17.5.tar.gz

apt-get install libpcre3-dev
apt-get install openssl
apt-get install  libssl-dev
apt-get install zlib1g-dev

./configure --prefix=/usr/local/nginx \  --with-http_stub_status_module \  --with-http_gzip_static_module\  --with-http_realip_module\  --with-http_sub_module \  --with-http_ssl_module\  --with-http_realip_module \  --with-http_sub_module \  --with-http_gunzip_module\  --with-http_gzip_static_module\  --with-http_auth_request_module\  --with-http_random_index_module  \  --with-http_slice_module \  --with-http_stub_status_module

make && make install

> 配置文件说明

main （全局设置）， main 部分设置的指令将影响其它所有部分的设置；

http(http服务器设置)，http标准核心模块，http服务的相应配置

server （主机设置）
接收请求的服务器需要将不同的请求按规则转发到不同的后端服务器上，在 nginx 中我们可以通过构建虚拟主机（ server ）的概念来将这些不同的服务配置隔 离

location （URL匹配特定位置后的设置）， location 部分用于匹配网页位置（比如，根目录“/”,“/images”,等等）, server 是对应一个域名进行的配置，而 location 是在一个域名下对更精细的路径进行配置

    1、配置文件由指令与指令块构成 
    2、每条指令以;分号结尾，指令与参数间以空格符号分隔 3、指令块以｛｝大括号将多条指令组织在一起 
    4、使用#符号添加注释，提高可读性 
    5、include语句允许组合多个配置文件以提升可维护性 6、使用$符号使用变量 
    7、部分指令的参数支持正则表达式
