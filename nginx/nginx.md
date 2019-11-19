# nginx
## 配置
```
# 设置用户
user nginx;

# 工作衍生进程数
worker_processes  1;

# 设置pid存放路径(pid是控制系统中重要文件)
# pid  logs/nginx.pid


# 错误日志目录
error_log /var/log/nginx/error.log;



# 设置最大连接数
events {
    worker_connections 1024;
}


http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    # 请求日志目录
    access_log  /var/log/nginx/access.log  main;

    # 提高服务器性能,反向代理的时候关闭
    sendfile            on;

    # 开启时数据包不会马上传送，等到数据包最大一次性传输出去，有助于解决网络堵塞
    tcp_nopush          on;
    # 和tcp_nopush相反,但都为开启时起到平衡作用 
    tcp_nodelay         on;
    # 响应超时时间
    send_timeout        10s

    keepalive_timeout   65;
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

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    
    include /etc/nginx/conf.d/*.conf;

    server {
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
