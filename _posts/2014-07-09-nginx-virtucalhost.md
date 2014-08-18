---
layout: post
title: [转]Nginx 虚拟主机 VirtualHost 配置
categories: nginx
---

##增加 Nginx 虚拟主机

1. 进入 /usr/local/nginx/conf/vhost 目录, 创建虚拟主机配置文件 demo.neoease.com.conf ({域名}.conf).
2. 打开配置文件, 添加服务如下:

       server {
        listen       80;
        server_name demo.neoease.com;
        index index.html index.htm index.php;
        root  /var/www/demo_neoease_com;

        log_format demo.neoease.com '$remote_addr - $remote_user [$time_local] $request'
        '$status $body_bytes_sent $http_referer '
        '$http_user_agent $http_x_forwarded_for';
        access_log  /var/log/demo.neoease.com.log demo.neoease.com;
       }
       
3. 打开 Nginx 配置文件 /usr/local/nginx/conf/nginx.conf, 在 http 范围引入虚拟主机配置文件如下:

        include vhost/*.conf;
        
        
4. 重启 Nginx 服务, 执行以下语句.

        service nginx restart

##让 Nginx 虚拟主机支持 PHP

在前面第 2 步的虚拟主机服务对应的目录加入对 PHP 的支持, 这里使用的是 FastCGI, 修改如下:

    server {
        listen       80;
        server_name demo.neoease.com;
        index index.html index.htm index.php;
        root  /var/www/demo_neoease_com;

        location ~ .*\.(php|php5)?$ {
            fastcgi_pass unix:/tmp/php-cgi.sock;
            fastcgi_index index.php;
            include fcgi.conf;
        }

        log_format demo.neoease.com '$remote_addr - $remote_user [$time_local] $request'
        '$status $body_bytes_sent $http_referer '
        '$http_user_agent $http_x_forwarded_for';
        access_log  /var/log/demo.neoease.com.log demo.neoease.com;
    }

##图片防盗链

图片作为重要的耗流量大的静态资源, 可能网站主并不希望其他网站直接引用, Nginx 可以通过 referer 来防止外站盗链图片.

    server {
        listen       80;
        server_name demo.neoease.com;
        index index.html index.htm index.php;
        root  /var/www/demo_neoease_com;

        # 这里为图片添加为期 1 年的过期时间, 并且禁止 Google, 百度和本站之外的网站引用图片
        location ~ .*\.(ico|jpg|jpeg|png|gif)$ {
            expires 1y;
            valid_referers none blocked demo.neoease.com *.google.com *.baidu.com;
            if ($invalid_referer) {
                return 404;
            }
        }

        log_format demo.neoease.com '$remote_addr - $remote_user [$time_local] $request'
        '$status $body_bytes_sent $http_referer '
        '$http_user_agent $http_x_forwarded_for';
        access_log  /var/log/demo.neoease.com.log demo.neoease.com;
    }
    
    
原文地址：http://www.neoease.com/nginx-virtual-host/