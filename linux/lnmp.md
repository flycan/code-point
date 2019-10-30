### LNMP环境的配置

安装顺序为Nginx、PHP、MySQL

### 安装PHP

#### 3.1 安装前准备

安装php拓展所需要的依赖。如gd库、zlib、curl等

```shell
yum -y install libmcrypt-devel mhash-devel libxslt-devel \
	libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel \
	zlib zlib-devel glibc glibc-devel glib2 glib2-devel bzip2 bzip2-devel \
	ncurses ncurses-devel curl curl-devel e2fsprogs e2fsprogs-devel \
	krb5 krb5-devel libidn libidn-devel openssl openssl-devel
```

#### 3.2 编译PHP

下载php的源码包 当前PHP最新版本是7.2.9

```shell
wget -c wget http://be2.php.net/get/php-7.2.9.tar.gz/from/this/mirror -O php-7.2.9.tar.gz
tar zxvf php-7.2.9.tar.gz
cd php-7.2.9

./configure --prefix=/usr/local/php \
	--enable-mbstring --enable-fpm --with-curl \
	--with-bz2  --with-zlib  \
	--enable-pcntl \
	--with-mhash --enable-zip  \
	--with-mysqli=mysqlnd \
	--with-pdo-mysql=mysqlnd \
	--with-gd --with-jpeg-dir --with-freetype-dir --with-png-dir \
	--with-apxs2=/usr/local/apache/bin/apxs \
make && make install
```
为php命令建立软链接
```bash
ln -s /usr/local/php7/bin/php /usr/local/bin/php
```
复制php.ini
```
cp php.ini-development /usr/local/php7/lib/php.ini
```
为php-fpm建立软链接
```
ln -s /usr/local/php7/sbin/php-fpm /usr/local/sbin/php-fpm
```
复制php-fpm.conf
```
cp /usr/local/php7/etc/php-fpm.conf.default /usr/local/php7/etc/php-fpm.conf
```
复制www.conf
```
cp /usr/local/php7/etc/php-fpm.d/www.conf.default /usr/local/php7/etc/php-fpm.d/www.conf
```

配置 php.ini
php_expose = Off
cgi.fix_pathinfo=0
vim /usr/local/php7/lib/php.ini

#### 编译安装PHP扩展

通过phpize可以建立php的外挂模块。
```
/usr/local/php7/bin/phpize
./configure --with-php-config=/usr/local/php7/bin/php-config
make && make install
```
autoconf可以自动地配置软件源代码

contos/redhat：`yum install autoconf`

ubuntu：`apt-get install autoconf`

### nginx
优点
1. IO多路复用epoll
2. 轻量级
3. CPU亲和性（affinity）好
4. sendfile
安装
1. yum方式安装 [详见nginx](http://nginx.org/en/linux_packages.html#stable)
创建/etc/yum.repos.d/nginx.repo文件，并输入如下内容
```
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/mainline/OS/OSRELEASE/$basearch/
gpgcheck=0
enabled=1
```

运行 `yum install -y nginx` 安装nginx

运行 `nginx -v` 查看nginx版本 

安装编译参数详解【[参考](http://nginx.org/en/docs/configure.html)】

| 编译选项                 | 作用                     |
| ----------------- | ---------------------------- |
| --prefix=/etc/nginx              | 配置文件目录               |
| --sbin-path=/usr/sbin/nginx      | 可执行文件名称和所在目录             |
| --modules-path=/usr/lib64/nginx/modules | nginx动态模块的安装目录         |
| --conf-path=/etc/nginx/nginx.conf      | 主配置文件名称和所在目录 |
| --error-log-path=/var/log/nginx/error.log       | 	全局错误日志文件名称和所在目录   |
| --http-log-path=/var/log/nginx/access.log              | HTTP服务器的主请求日志文件的名称和所在目录   |
| --pid-path=/var/run/nginx.pid 		| nginx.pid所在目录，这是储存主进程的进程ID文件 |
| --lock-path=/var/run/nginx.lock 		| nginx.lock所在目录 |
|--http-client-body-temp-path=/var/cache/nginx/client_temp<br>--http-proxy-temp-path=/var/cache/nginx/proxy_temp<br>--http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp<br>--http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp<br>--http-scgi-temp-path=/var/cache/nginx/scgi_temp | 执行对应模块时nginx所保留的临时文件 |
| --user=nginx<br>--group=nginx  | 设定Nginx进程启动的用户和用户组 |
| --with-http_random_index_module | 目录中随机选择一个随机主页 |
| --with-http_stub_status_module | Nginx客户端状态 |
| --with-http_sub_module | HTTP内容替换 |
| --with-cc-opt=parameters | 设置额外的参数将被添加到CFLAGS变量 |
| --with-ld-opt=parameters | 设置附加参数，链接系统库 |

Nginx变量

- `arg_参数名`：例如，$arg_userid，可以引用到请求参数userid的值
- `http_请求HEADER名`：例如，$http_user_agent，可以引用到请求头信息User-Agent的值
- `sent_http_返回HEADER名`：在响应客户端可添加header头信息

内置变量 参考Nginx文档的 [Logging to syslog](http://nginx.org/en/docs/syslog.html)

```
nginx的配置文件中可以使用的内置变量以美元符$开始，也有人叫全局变量。其中，部分预定义的变量的值是可以改变的。

$arg_PARAMETER 这个变量值为：GET请求中变量名PARAMETER参数的值。

$args 这个变量等于GET请求中的参数。例如，foo=123&bar=blahblah;这个变量只可以被修改

$binary_remote_addr 二进制码形式的客户端地址。

$body_bytes_sent 传送页面的字节数

$content_length 请求头中的Content-length字段。

$content_type 请求头中的Content-Type字段。

$cookie_COOKIE cookie COOKIE的值。

$document_root 当前请求在root指令中指定的值。

$document_uri 与$uri相同。

$host 请求中的主机头(Host)字段，如果请求中的主机头不可用或者空，则为处理请求的server名称(处理请求的server的server_name指令的值)。值为小写，不包含端口。

$hostname  机器名使用 gethostname系统调用的值

$http_HEADER   HTTP请求头中的内容，HEADER为HTTP请求中的内容转为小写，-变为_(破折号变为下划线)，例如：$http_user_agent(Uaer-Agent的值), $http_referer...;

$sent_http_HEADER  HTTP响应头中的内容，HEADER为HTTP响应中的内容转为小写，-变为_(破折号变为下划线)，例如： $sent_http_cache_control, $sent_http_content_type...;

$is_args 如果$args设置，值为"?"，否则为""。

$limit_rate 这个变量可以限制连接速率。

$nginx_version 当前运行的nginx版本号。

$query_string 与$args相同。

$remote_addr 客户端的IP地址。

$remote_port 客户端的端口。

$remote_user 已经经过Auth Basic Module验证的用户名。

$request_filename 当前连接请求的文件路径，由root或alias指令与URI请求生成。

$request_body 这个变量（0.7.58+）包含请求的主要信息。在使用proxy_pass或fastcgi_pass指令的location中比较有意义。

$request_body_file 客户端请求主体信息的临时文件名。

$request_completion 如果请求成功，设为"OK"；如果请求未完成或者不是一系列请求中最后一部分则设为空。

$request_method 这个变量是客户端请求的动作，通常为GET或POST。

包括0.8.20及之前的版本中，这个变量总为main request中的动作，如果当前请求是一个子请求，并不使用这个当前请求的动作。

$request_uri 这个变量等于包含一些客户端请求参数的原始URI，它无法修改，请查看$uri更改或重写URI。

$scheme 所用的协议，比如http或者是https，比如rewrite ^(.+)$ $scheme://example.com$1 redirect;

$server_addr 服务器地址，在完成一次系统调用后可以确定这个值，如果要绕开系统调用，则必须在listen中指定地址并且使用bind参数。

$server_name 服务器名称。

$server_port 请求到达服务器的端口号。

$server_protocol 请求使用的协议，通常是HTTP/1.0或HTTP/1.1。

$uri 请求中的当前URI(不带请求参数，参数位于$args)，不同于浏览器传递的$request_uri的值，它可以通过内部重定向，或者使用index指令进行修改。不包括协议和主机名，例如/foo/bar.html
```

Nginx常用命令

| 命令 | 解释|
| ----------------- | ---------------------------- |
| `nginx [-c <配置文件>]` | 以指定的配置文件启动nginx |
| `nginx -s quit` | 正常停止nginx，Nginx在退出前完成已经接受的连接请求。 |
| `nginx -s stop` | 快速停止nginx，不管有没有正在处理的请求。 |
| `nginx -s reload` [-c <配置文件>] | 重载配置文件 |
| `nginx -s reopen` | 重新打开日志文件 |
| `nginx -v` | 查看版本 |
| `nginx -V` | 查看安装时的编译参数 |
| `nginx -t [-c <配置文件>]` | 检查配置文件语法是否正确 |


```
# nginx运行的用户
user  nginx;
# nginx进程数，建议设置为等于CPU总核心数。
worker_processes  1;

# 全局错误日志文件名称和所在目录，错误日志记录级别[ debug | info | notice | warn | error | crit ]
error_log  /var/log/nginx/error.log warn;
# 进程文件nginx.pid所在目录
pid        /var/run/nginx.pid;

# 一个nginx进程打开的最多文件描述符数目，
# 理论值应该是最多打开文件数（系统的值ulimit -n）与nginx进程数相除，
# 但是nginx分配请求并不均匀，所以建议与ulimit -n的值保持一致。
# worker_rlimit_nofile 65535;

events {
    # 参考事件模型，use [ kqueue | rtsig | epoll | /dev/poll | select | poll ]; 
    # epoll模型是Linux 2.6以上版本内核中的高性能网络I/O模型，如果跑在FreeBSD上面，就用kqueue模型。
    # use epoll;

    # 单个进程最大连接数（最大连接数 = 连接数 * 进程数）
    worker_connections  1024;
    
    # multi_accept on;
}

# http服务器配置
http {
    # 引入http协议的Content-Type与扩展名对应关系的文件
    include       /etc/nginx/mime.types;
    # 默认文件类型
    default_type  application/octet-stream;
    
    # 指定日志格式
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    
    # HTTP服务器请求日志文件的名称和所在目录、日志记录级别
    access_log  /var/log/nginx/access.log  main;

    # 开启高效文件传输模式。
    # sendfile指令指定nginx是否调用sendfile函数来输出文件，对于普通应用设为on，
    # 如果用来进行下载等应用磁盘IO重负载应用，可设置为off，以平衡磁盘与网络I/O处理速度，降低系统的负载。
    # 注意：如果图片显示不正常把这个改成off。
    sendfile        on;
    # 防止网络阻塞
    # tcp_nopush    on;
    # tcp_nodelay   on;

    # 开启目录列表访问，合适下载服务器，默认关闭。
    # autoindex on; 
    
    # 开启限制IP连接数的时候需要使用
    # limit_zone crawler $binary_remote_addr 10m; 
    
    # 长连接超时时间，单位是秒
    keepalive_timeout  65;

    # gzip模块设置
    # 开启gzip压缩输出
    # gzip  on;
    # 最小压缩文件大小
    # gzip_min_length 1k; 
    # 压缩缓冲区
    # gzip_buffers 4 16k; 
    # 压缩版本（默认1.1，前端如果是squid2.5请使用1.0）
    # gzip_http_version 1.0; 
    # 压缩等级
    # gzip_comp_level 2; 
    # 压缩类型，默认就已经包含text/html，所以下面就不用再写了，写上去也不会有问题，但是会有一个warn。
    # gzip_types text/plain application/x-javascript text/css application/xml;
    # gzip_vary on;
        
     
    # FastCGI相关参数是为了改善网站的性能：减少资源占用，提高访问速度。
    # fastcgi_connect_timeout 300;
    # fastcgi_send_timeout 300;
    # fastcgi_read_timeout 300;
    # fastcgi_buffer_size 128k;
    # fastcgi_buffers 8 128k;
    # fastcgi_busy_buffers_size 128k;
    # fastcgi_temp_file_write_size 128k;

    # 允许客户端请求的最大单文件字节数。 
    # 如果请求大于指定的大小，则NGINX发回HTTP 413（Request Entity too large）错误。 
    # 如果服务器处理大文件上传，则该指令非常重要。
    # client_max_body_size 20m;
    # 用于请求主体的缓冲区大小。 
    # 如果主体超过缓冲区大小，则完整主体或其一部分将写入临时文件。 
    # 如果NGINX配置为使用文件而不是内存缓冲区，则该指令会被忽略。 
    # 默认情况下，该指令为32位系统设置一个8k缓冲区，为64位系统设置一个16k缓冲区。 
    # 该指令在NGINX配置的http，server和location区块使用。
    # client_body_buffer_size 128k;
    
    # upstream abc.com {
        # upstream的负载均衡，weight是权重，可以根据机器配置定义权重。weigth参数表示权值，权值越高被分配到的几率越大。
    #     server 192.168.10.121:80 weight=3;
    #     server 192.168.10.122:80 weight=2;
    #     server 192.168.10.123:80 weight=3;
    # }
    
    # 引入 '/etc/nginx/conf.d/' 目录下以 '.conf' 结尾的文件
    include /etc/nginx/conf.d/*.conf;
    
    # 虚拟主机的配置
    server {
        # 监听端口
        listen       80;
        # 域名，可以有多个，用空格隔开
        server_name  localhost;
        
        # 默认编码
        # charset utf-8; 
        
        # 日志格式设定
        # log_format access '$remote_addr - $remote_user [$time_local] "$request" '
        # '$status $body_bytes_sent "$http_referer" '
        # '"$http_user_agent" $http_x_forwarded_for';
        # 当前虚拟主机请求日志文件的名称和所在目录、日志记录级别
        # access_log  /var/log/nginx/host.access.log  main;

        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm;
        }
    
        #error_page  404              /404.html;
    
        # 将服务器错误页面重定向到静态页面 /50x.html
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
    
        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        # 将 PHP 脚本反向代理到监听 127.0.0.1:80 端口的Apache
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}
    
        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        # 将 PHP 脚本反向代理到监听127.0.0.1:9000端口的FastCGI服务器
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}
    
        # 如果Apache的文档根目录与nginx的相同，拒绝访问.htaccess文件。
        #location ~ /\.ht {
        #    deny  all;
        #}
        
        # 图片缓存时间设置
        # location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$ {
        #     expires 10d;
        # }
        
        # JS和CSS缓存时间设置
        # location ~ .*\.(js|css)?$ {
        #     expires 1h;
        # }
        
		location ~* ^/(css|img|js|flv|swf|download)/(.+)$ {
        	root /srv/www/htdocs/phalcon-website/public;
    	}

        # 对 "/" 启用反向代理
        # location / {
        #     proxy_pass http://127.0.0.1:88;
        #     proxy_redirect off;
        #     # 后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
        #     proxy_set_header X-Real-IP $remote_addr;
        #     proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        #     # 以下是一些反向代理的配置，可选。
        #     proxy_set_header Host $host;
        #     # nginx跟后端服务器连接超时时间(代理连接超时)
        #     proxy_connect_timeout 90; 
        #     # 后端服务器数据回传时间(代理发送超时)
        #     proxy_send_timeout 90; 
        #     # 连接成功后，后端服务器响应时间(代理接收超时)
        #     proxy_read_timeout 90; 
        #     # 设置代理服务器（nginx）保存用户头信息的缓冲区大小
        #     proxy_buffer_size 4k; 
        #     # proxy_buffers缓冲区，网页平均在32k以下的设置
        #     proxy_buffers 4 32k; 
        #     # 高负荷下缓冲大小（proxy_buffers*2）
        #     proxy_busy_buffers_size 64k; 
        #     # 设定缓存文件夹大小，大于这个值，将从upstream服务器传
        #     proxy_temp_file_write_size 64k;
        # }
    }
}
```