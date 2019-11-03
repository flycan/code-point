# nginx 动态负载均衡

nginx-upsync-module 提供了动态的负载均衡，动态更新上游的服务器不需要  reload nginx ，它的功能是拉取  consul 的后端  server 的列表，并更新
Nginx 的路由信息。此模块不依赖于任何第三方模块。  consul 作为  Nginx 的 db，利用  consul 的 KV 服务，每个  Nginx work 进程独立的去拉取各个
upstream 的配置，并更新各自的路由。
挂了第三方模块无法结束进程或者是当前进程尚未结束。
nginx -s reload 是平滑重启，不会强制结束正在工作的连接，需要等所有连接都结束才会重启。想象一个场景上线繁忙，想要多添加几台服务器处理更多的
流量
改配置文件并重新启动  Nginx 可能并不总是很方便。 例如，当遇到大流量和高负载，重启  Nginx 并在此时重新加载配置会进一步增加系统负载，并可能暂时
降低性能

```
wget https://github.com/weibocom/nginx-upsync-module/archive/v2.1.0.tar.gz
--add-module=/root/nginx-upsync-module-2.1.0
示例：
upstream swoole_test {
upsync 127.0.0.1:8700/v1/kv/upstreams/swoole_test upsync_timeout=6m upsync_interval=500ms upsync_type=consul
strong_dependency=off;
upsync_dump_path /usr/local/nginx/conf/servers_test.conf;
include /usr/local/nginx/conf/servers_test.conf;
}
upsync模块会去consul拉取最新的upstream信息并存到本地的文件中
upsync_timeout 配置从consul拉取上游服务器的超时时间
upsync_interval 配置从consul拉取上游服务器的间隔时间
upsync_type 指定使用配置服务器的类型，当前是consul
strong_dependency 启动时是否强制依赖配置服务器，如果配置为on,则拉取失败，nginx同样会启用失败
upsync_dump_path 指定从consul拉取的上游服务器后持久化到的位置，这样即使
Consul服务器出问题了，本地同样会有备份
```

安装步骤参考Lua模块的安装