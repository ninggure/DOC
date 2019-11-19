# nginx
## 配置
```
# 设置用户
user nginx;

# 工作衍生进程数
worker_processes  1;

# 设置pid存放路径(pid是控制系统中重要文件)
# pid logs/nginx.pid


# 设置最大连接数
events {
    worker_connections 1024;
}


http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
   
    # 提高服务器性能
    sendfile            on;

    # 开启时数据包不会马上传送，等到数据包最大一次性传输出去，有助于解决网络堵塞
    tcp_nopush          on;
    # 和tcp_nopush相反,但都为开启时起到平衡作用 
    tcp_nodelay         on;
    # 响应超时时间
    send_timeout        10s

    keepalive_timeout   60s;
    # 影响散列表的冲突率:
    # types_hash_max_size越大，就会消耗更多的内存，但散列key的冲突率会降低，检索速度就更快;
    # types_hash_max_size越小，消耗的内存就越小，但散列key的冲突率可能上升。
    types_hash_max_size 2048;

    # gzip
    # 开启压缩，减少数据传输量
    gzip                on;
    gzip_min_length     1k;
    # 用于设置在使用Gzip功能时是否发送带有“Vary：Accept-Encoding”头域的响应头部
    gzip_vary           on;

    include             mime.types;
    default_type        application/octet-stream;
    # 导入多个server配置
    include /etc/nginx/conf.d/*.conf;
    
    server{
        listen 80 default_server;
        return 404;
    }
    
    server{
        listen       80;
        server_name  localhost;
        
        location /{
            root  html;
            index index.html index.htm
        }
        
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
    # 后面可配置多个server，可绑定同一个端口
    server {
        # 请求日志目录
        access_log  /var/log/nginx/access.log;
        # 错误日志目录
        error_log /var/log/nginx/error.log; 

    
        listen       80 default_server;
        listen       [::]:80 default_server;
        charset      utf8
        server_name  localhost;
        # root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
            proxy_pass http://172.17.0.3:8080/;
        }

        location  /_next/static/ {
            alias /usr/share/nginx/html/static/;
        }
    }
}
```
## 优化
-   [内核优化](./modules/Kernel.md)
-   [nginx优化](./modules/nginx.md)
-   [gzip](./modules/gzip.md)
