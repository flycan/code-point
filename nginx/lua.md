# NGINX 安装 Lua 模块

centos环境

## 1. 安装依赖
    
    yum -y install gcc zlib zlib-devel pcre-devel openssl openssl-devel
    
## 2. 安装LuaJIT

```bash
cd /usr/local/src
wget http://luajit.org/download/LuaJIT-2.0.5.tar.gz
tar -zxcf LuaJIT-2.0.5.tar.gz
cd LuaJIT-2.0.5
make install

## 需要修改环境变量 可以直接修改 /etc/profile 文件
export LUAJIT_LIB=/usr/local/lib 
export LUAJIT_INC=/usr/local/include/luajit-2.0 
ln -sf /usr/local/lib/libluajit-5.1.so.2 /lib64/libluajit-5.1.so.2

参考地址： https://blog.csdn.net/u014042372/article/details/82884368
```

## 3. 安装 nginx

```bash
cd /usr/local/src
## 下载 ngx_devel_kit
wget https://github.com/simpl/ngx_devel_kit/archive/v0.3.0.tar.gz
## 下载 lua-nginx-module
wget https://github.com/openresty/lua-nginx-module/archive/v0.10.15.tar.gz
## 下载nginx
wget http://nginx.org/download/nginx-1.12.2.tar.gz


## 解压文件
tar -zxvf v0.3.0.tar.gz
tar -zxvf v0.10.15.tar.gz
tar -zxvf nginx-1.12.2.tar.gz

## 安装
cd nginx-1.12.2
./configure --prefix=/usr/local/nginx --add-module=../ngx_devel_kit-0.3.0 --add-module=../lua-nginx-module-0.10.15
make && make install

安装报错
objs/nginx: error while loading shared libraries: libluajit-5.1.so.2: cannot open shared object file: No such file or directory
解决方法：
# ln -s /usr/local/lib/libluajit-5.1.so.2 /lib64/libluajit-5.1.so.2

```

#### 启动nginx 

```bash
    cd /usr/local/nginx/sbin
    ./nginx

    ## 输出如下错误信息    
    nginx: [alert] detected a LuaJIT version which is not OpenResty's; many optimizations will be disabled and performance will be compromised (see https://github.com/openresty/luajit2 for OpenResty's LuaJIT or, even better, consider using the OpenResty releases from https://openresty.org/en/download.html)
    nginx: [error] lua_load_resty_core failed to load the resty.core module from https://github.com/openresty/lua-resty-core; ensure you are using an OpenResty release from https://openresty.org/en/download.html (rc: 2, reason: module 'resty.core' not found:
    	no field package.preload['resty.core']
    	no file '/usr/local/lib/lua/resty/core.lua'
    	no file './resty/core.lua'
    	no file '/usr/local/luajit/share/luajit-2.0.5/resty/core.lua'
    	no file '/usr/local/share/lua/5.1/resty/core.lua'
    	no file '/usr/local/share/lua/5.1/resty/core/init.lua'
    	no file '/usr/local/luajit/share/lua/5.1/resty/core.lua'
    	no file '/usr/local/luajit/share/lua/5.1/resty/core/init.lua'
    	no file './resty/core.so'
    	no file '/usr/local/lib/lua/5.1/resty/core.so'
    	no file '/usr/local/luajit/lib/lua/5.1/resty/core.so'
    	no file '/usr/local/lib/lua/5.1/loadall.so'
    	no file './resty.so'
    	no file '/usr/local/lib/lua/5.1/resty.so'
    	no file '/usr/local/luajit/lib/lua/5.1/resty.so'
    	no file '/usr/local/lib/lua/5.1/loadall.so')
    	
    查看服务nginx 发现nginx版本很低 不管什么原因  先升级试试看
    下载最新版1.17.2
    cd /usr/local/src
    wget http://nginx.org/download/nginx-1.17.2.tar.gz
    tar -zxvf nginx-1.17.2.tar.gz
    cd nginx-1.17.2
    ./configure --prefix=/usr/local/nginx --add-module=../ngx_devel_kit-0.3.0 --add-module=../lua-nginx-module-0.10.15
    make && make install
```

升级完成后继续测试
    
    cd /usr/local/nginx/sbin
    ./nginx
    TMD依然报错
    nginx: [error] lua_load_resty_core failed to load the resty.core module from https://github.com/openresty/lua-resty-core; ensure you are using an OpenResty release from https://openresty.org/en/download.html (rc: 2, reason: module 'resty.core' not found:
	no field package.preload['resty.core']
	no file '/usr/local/lib/lua/resty/core.lua'
	no file './resty/core.lua'
	no file '/usr/local/share/luajit-2.1.0-beta3/resty/core.lua'
	no file '/usr/local/share/lua/5.1/resty/core.lua'
	no file '/usr/local/share/lua/5.1/resty/core/init.lua'
	no file './resty/core.so'
	no file '/usr/local/lib/lua/5.1/resty/core.so'
	no file '/usr/local/lib/lua/5.1/loadall.so'
	no file './resty.so'
	no file '/usr/local/lib/lua/5.1/resty.so'
	no file '/usr/local/lib/lua/5.1/loadall.so')

    百度结果 是 lua-nginx-module 0.10.15 的版本有问题 需要使用0.10.14
    http://www.bubuko.com/infodetail-3027124.html
    https://oneinstack.com/question/18799/
    
下载 lua-nginx-module 0.10.14
    cd /usr/local/src
    wget https://github.com/openresty/lua-nginx-module/archive/v0.10.14.tar.gz
    tar -zxvf v0.10.14.tar.gz
    
更新nginx的模块

```bash
    cd /usr/local/src/nginx-1.17.2
    ./configure --prefix=/usr/local/nginx --add-module=../ngx_devel_kit-0.3.0 --add-module=../lua-nginx-module-0.10.14
    make
    不能执行 make install 否则会完全覆盖
    cd objs
    cp /usr/local/nginx/sbin/nginx /usr/local/nginx/sbin/nginx.bak
    /usr/local/nginx/sbin/nginx -s stop
    cp nginx /usr/local/nginx/sbin/nginx
```

Redis 模块的安装

```
cd /usr/local/nginx/sbin
./nginx -V 
## 查看当前的nginx参数信息
nginx version: nginx/1.17.2
built by gcc 4.8.5 20150623 (Red Hat 4.8.5-36) (GCC) 
configure arguments: --prefix=/usr/local/nginx --add-module=../ngx_devel_kit-0.3.1 --add-module=../lua-nginx-module-0.10.14 --add-module=../redis2-nginx-module


cd /usr/local/src
git clone https://github.com/openresty/redis2-nginx-module.git
cd nginx-1.17.2
./configure --prefix=/usr/local/nginx --add-module=../ngx_devel_kit-0.3.1 --add-module=../lua-nginx-module-0.10.14 --add-module=../redis2-nginx-module
make
不能执行 make install 否则会完全覆盖
cd objs
cp /usr/local/nginx/sbin/nginx /usr/local/nginx/sbin/nginx.bak
/usr/local/nginx/sbin/nginx -s stop
cp nginx /usr/local/nginx/sbin/nginx
```
至此redis模块也安装完毕
redis模块支持连接池 订阅等功能
redis模块的使用参考官方 [github](https://github.com/openresty/redis2-nginx-module/)


参考：

https://www.cnblogs.com/LiQ0116/p/9174089.html

https://blog.csdn.net/qq_38974634/article/details/81625075

http://www.ttlsa.com/nginx/nginx-modules-ngx_lua/

https://blog.csdn.net/u014042372/article/details/82884368

http://luajit.org/download.html


Redis模块的安装参考

https://www.jianshu.com/p/465dc29b3a61

https://zixuephp.net/article-460.html

https://github.com/openresty/redis2-nginx-module

nginx+redis实现接入层高性能缓存技术

https://www.cnblogs.com/hongmoshui/p/10232058.html
    