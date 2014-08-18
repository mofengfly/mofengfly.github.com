---
layout: post
title: nginx反向代理
categories: nginx
---

Nginx可以代理请求，将请求发送到代理服务器上，然后获取response，再将其发送回客户端。可以代理请求道一个http服务器（另外一个nginx服务器或是其它服务器）或者使用特定协议的非http服务器，支持的协议包括FastCGI, uwsgi, SCGI, and memcached.

###正向代理的概念

  正向代理，也就是传说中的代理,他的工作原理就像一个跳板，简单的说，我是一个用户，我访问不了某网站，但是我能访问一个代理服务器，这个代理服务器呢，他能访问那个我不能访问的网站，于是我先连上代理服务器，告诉他我需要那个无法访问网站的内容，代理服务器去取回来，然后返回给我。从网站的角度，只在代理服务器来取内容的时候有一次记录，有时候并不知道是用户的请求，也隐藏了用户的资料，这取决于代理告不告诉网站。
   
   正向代理 是一个位于客户端和原始服务器(origin server)之间的服务器，为了从原始服务器取得内容，客户端向代理发送一个请求并指定目标(原始服务器)，然后代理向原始服务器转交请求并将获得的内容返回给客户端。客户端必须要进行一些特别的设置才能使用正向代理。a
      
###反向代理的概念

反向代理正好相反，对于客户端而言它就像是原始服务器，并且客户端不需要进行任何特别的设置。客户端向反向代理的命名空间(name-space)中的内容发送普通请求，接着反向代理将判断向何处(原始服务器)转交请求，并将获得的内容返回给客户端，就像这些内容原本就是它自己的一样。

从用途上来讲：正向代理的典型用途是为在防火墙内的局域网客户端提供访问Internet的途径。正向代理还可以使用缓冲特性减少网络使用率。反向代理的典型用途是将防火墙后面的服务器提供给Internet用户访问。反向代理还可以为后端的多台服务器提供负载平衡，或为后端较慢的服务器提供缓冲服务。另外，反向代理还可以启用高级URL策略和管理技术，从而使处于不同web服务器系统的web页面同时存在于同一个URL空间下。

从安全性来讲：正向代理允许客户端通过它访问任意网站并且隐藏客户端自身，因此你必须采取安全措施以确保仅为经过授权的客户端提供服务。反向代理对外都是透明的，访问者并不知道自己访问的是一个代理。

###HTTP代理模块

中文文档在[这里](http://www.howtocn.org/nginx:nginx%E6%A8%A1%E5%9D%97%E5%8F%82%E8%80%83%E6%89%8B%E5%86%8C%E4%B8%AD%E6%96%87%E7%89%88:standardhttpmodules:httpproxy)



####[proxy_pass](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_pass)

用于配置代理，设置被代理服务器的地址和被映射的URI，在一个location中配置，例如：

    location /some/path/ {
        proxy_pass http://www.example.com/link/;
    }
 
 
这个配置会把这个位置的所有请求转发到特定地址的代理服务器。地址可以用域名或是IP地址设置，可以包括端口：

    location ~ \.php {
        proxy_pass http://127.0.0.1:8000;
    }
  
第一个例子中，代理服务器地址后面跟着/link。如果设置URI的时候带着地址，它会替换匹配location参数部分的请求URI。因此，上例中，如果请求 /some/path/page.html，URI将会代理到http://www.example.com/link/page.html

让被代理服务器接受到的请求的客户端IP显示实际的IP而不是代理服务器的IP

    location / {
            proxy_pass      http://192.168.18.201;
            proxy_set_header  X-Real-IP  $remote_addr; #加上这一行
    }

注意:当使用http proxy模块（甚至FastCGI），所有的连接请求在发送到后端服务器之前nginx将缓存它们。

####proxy_set_header
参考资源

http://nginx.com/resources/admin-guide/reverse-proxy/

http://freeloda.blog.51cto.com/2033581/1288553

http://www.ha97.com/5194.html