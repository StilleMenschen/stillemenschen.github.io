# SSL 配置

```nginx
# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/

user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

# Load dynamic modules. See /usr/share/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

stream {
    #log_format basic '$remote_addr [$time_local] '
    #             '$protocol $status $bytes_sent $bytes_received '
    #             '$session_time';

    #access_log logs/nginx-access.log basic buffer=32k;

    upstream backend {
        hash $remote_addr consistent;

        server backend1.example.com:12345 weight=5;
        server 127.0.0.1:12345            max_fails=3 fail_timeout=30s;
        server unix:/tmp/backend3;
    }

    upstream dns {
       server 192.168.0.1:53535;
       server dns.example.com:53;
    }

    server {
        listen 12345;
        proxy_connect_timeout 1s;
        proxy_timeout 3s;
        proxy_pass backend;
    }

    server {
        listen 127.0.0.1:53 udp reuseport;
        proxy_timeout 20s;
        proxy_pass dns;
    }

    server {
        listen [::1]:12345;
        proxy_pass unix:/tmp/stream.socket;
    }
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   70;
    types_hash_max_size 2048;
    server_tokens  off; # 响应头中不显示具体的版本号
    client_max_body_size 128m; # 请求大小限制

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

    # 开启gzip压缩以加快传输速度，静态文件必须有同名称并以.gz结尾的压缩文件
    gzip on;
    gzip_min_length  128k;
    gzip_buffers     4 16k;
    gzip_comp_level  4;
    gzip_types       text/plain application/javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png image/svg+xml;
    gzip_vary on;

    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  localhost;
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        # 如果不想开启非加密链接访问，可以直接使用301永久重定向到加密链接
        # return 301 https://$server_name$request_uri;

        location / {
            root     /opt/webroot;
            index    index.html index.htm;
            fastcgi_read_timeout 500;
        }

        # 部分静态文件的过期时间设置为6小时
        location ~* \.(ico|jpg|png|js|css)$ {
             root    /opt/webroot;
             expires 6h;
        }

        error_page 404 /40x.html;
        location = /40x.html {
            root /opt/webroot;
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
            root /opt/webroot;
        }
    }

    server {
        listen         443 ssl http2 default_server;
        listen         [::]:443 ssl http2 default_server;
        server_name    www.example.com;

        # 如果域名不匹配则拒绝访问
        if ($host != 'www.example.com') { return 403; }

        ssl_certificate      /opt/ssl/www.example.com.pem;  #指定数字证书文件
        ssl_certificate_key  /opt/ssl/www.example.com.key;  #指定数字证书私钥文件
        ssl_session_cache    shared:SSL:10m;
        ssl_session_timeout  10m;
        ssl_protocols        TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers          ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DH-DSS-AES256-GCM-SHA384:DHE-DSS-AES256-GCM-SHA384:DH-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA256:DH-RSA-AES256-SHA256:DH-DSS-AES256-SHA256:DHE-RSA-AES256-SHA:DHE-DSS-AES256-SHA:DH-RSA-AES256-SHA:DH-DSS-AES256-SHA:DHE-RSA-CAMELLIA256-SHA:DHE-DSS-CAMELLIA256-SHA:DH-RSA-CAMELLIA256-SHA:DH-DSS-CAMELLIA256-SHA:ECDH-RSA-AES256-GCM-SHA384:ECDH-ECDSA-AES256-GCM-SHA384:ECDH-RSA-AES256-SHA384:ECDH-ECDSA-AES256-SHA384:ECDH-RSA-AES256-SHA:ECDH-ECDSA-AES256-SHA:AES256-GCM-SHA384:AES256-SHA256:AES256-SHA:CAMELLIA256-SHA:PSK-AES256-CBC-SHA:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:DH-DSS-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:DH-RSA-AES128-GCM-SHA256:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES128-SHA256:DHE-DSS-AES128-SHA256:DH-RSA-AES128-SHA256:DH-DSS-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA:DH-RSA-AES128-SHA:DH-DSS-AES128-SHA:DHE-RSA-SEED-SHA:DHE-DSS-SEED-SHA:DH-RSA-SEED-SHA:DH-DSS-SEED-SHA:DHE-RSA-CAMELLIA128-SHA:DHE-DSS-CAMELLIA128-SHA:DH-RSA-CAMELLIA128-SHA:DH-DSS-CAMELLIA128-SHA:ECDH-RSA-AES128-GCM-SHA256:ECDH-ECDSA-AES128-GCM-SHA256:ECDH-RSA-AES128-SHA256:ECDH-ECDSA-AES128-SHA256:ECDH-RSA-AES128-SHA:ECDH-ECDSA-AES128-SHA:AES128-GCM-SHA256:AES128-SHA256:AES128-SHA:SEED-SHA:CAMELLIA128-SHA:PSK-AES128-CBC-SHA:ECDHE-RSA-DES-CBC3-SHA:ECDHE-ECDSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:EDH-DSS-DES-CBC3-SHA:DH-RSA-DES-CBC3-SHA:DH-DSS-DES-CBC3-SHA:ECDH-RSA-DES-CBC3-SHA:ECDH-ECDSA-DES-CBC3-SHA:DES-CBC3-SHA:IDEA-CBC-SHA:PSK-3DES-EDE-CBC-SHA:KRB5-IDEA-CBC-SHA:KRB5-DES-CBC3-SHA:KRB5-IDEA-CBC-MD5:KRB5-DES-CBC3-MD5:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:ECDH-RSA-RC4-SHA:ECDH-ECDSA-RC4-SHA:RC4-SHA:RC4-MD5:PSK-RC4-SHA:KRB5-RC4-SHA:KRB5-RC4-MD5;
        ssl_prefer_server_ciphers  on;

        location / {
             root    /opt/webroot;
             index   index.html index.htm;
        }

        location ~* \.(ico|jpg|png|js|css)$ {
             root    /opt/webroot;
             expires 6h;
        }

        error_page 497 https://$server_name$request_uri; # 如果不是以https访问强制跳转为https访问

        error_page 404 /40x.html;
        location = /40x.html {
            root /opt/webroot;
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
            root /opt/webroot;
        }
    }
}
```

Last Modified 2021-03-19
