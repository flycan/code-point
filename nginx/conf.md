# nginx 示例配置

```
#user  nobody;
worker_processes  2;
worker_priority -15;  #woker进程优先级
worker_cpu_affinity auto;  #自动绑定cpu跟进程的关系
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;


#pid        logs/nginx.pid;

events {
    worker_connections  100000; #设置单个worker连接数
}


http {
    include       mime.types;

    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;



    #判断客户端的ip地址是否在白名单列表当中,如果返回为0,则在白名单列表当中,否则返回为1
    geo $whiteIpList {
        default  1;
        192.168.10.1 0;
        192.168.1.0/24 1;
        #include '/conf/whiteIP.conf'
    }
    #如果不在白名单之内,返回客户端的二进制的ip地址
    map $whiteIpList  $limit {
        default  "";
        1   $binary_remote_addr;
        0   "";
    }
    #如果返回的是空字符串那么速率限制会失效
    limit_req_zone $limit zone=test:1m rate=1r/s;

    server {
        listen       80;
        #server_name  www.test.com;
        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        root html;
        index  index.html;

        #速率限制
        location /req {
            imit_req zone=test burst=5 nodelay;
            default_type text/html;
            return  200  '$remote_addr';
        }

        # rewrite if return 重写
        location /mobile {
             #return  200  '$http_user_agent';
             if ($http_user_agent ~* (mobile|nokia|iphone|ipad|android|samsung|htc|blackberry)) {
                  rewrite ^(.*) http://h5.23753.com$1 permanent;
             }
        }

        #ip限制
        #location / {
        #    allow 118.24.109.254;
        #    deny  47.98.147.0/24;
        #    deny all;
        #}

        location ~.*\.(sql|log|txt|jar|war|sh|py|php) {
            deny all;
        }


        location /file {
              root /www;
        }

        #指定静态文件的路径
        location ~* \.(gif|jpg|png|jpeg) {
             alias /www/test; #绝对路径

        }



        location /test {
                        default_type text/html;
                        return  200  'test2';
        }
        location =/test {
                default_type text/html;
                return  200  'test1';
         }



        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }

    server {
            listen       81;
            #server_name  test.23673.com;
            #charset koi8-r;

            #access_log  logs/host.access.log  main;

            location / {
                root   html;
                index  index.html index.htm;
            }
            #error_page  404              /404.html;

            # redirect server error pages to the static page /50x.html
            #
            error_page   500 502 503 504  /50x.html;
            location = /50x.html {
                root   html;
            }

            # proxy the PHP scripts to Apache listening on 127.0.0.1:80
            #
            #location ~ \.php$ {
            #    proxy_pass   http://127.0.0.1;
            #}

            # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
            #
            #location ~ \.php$ {
            #    root           html;
            #    fastcgi_pass   127.0.0.1:9000;
            #    fastcgi_index  index.php;
            #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
            #    include        fastcgi_params;
            #}

            # deny access to .htaccess files, if Apache's document root
            # concurs with nginx's one
            #
            #location ~ /\.ht {
            #    deny  all;
            #}
        }

    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}


```