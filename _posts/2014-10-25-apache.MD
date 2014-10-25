---
layout: post
title: xmapp在IOS上的配置
description: 
categories: ios
---

1.安装

下载地址：https://www.apachefriends.org/download.html#849

2.配置域名

sudo vim /etc/hosts

3. 配置域名在Apache上对应的目录

修改httpd.conf文件，目录是/Applications/XAMPP/xamppfiles/etc/httpd.conf

搜索 “httpd-vhosts.conf”，去掉前面的 # 注释符，确保引入了 vhosts 虚拟主机配置文件。

在文件最后增加，解决403的问题

```
<Directory "/Users/heiniuhaha/Sites/project">
        #Options Indexes FollowSymLinks ExecCGI Includes #don't permission see list
        Options All
        AllowOverride All
        Order allow,deny
        Allow from all
</Directory>

```
我试了下上面了，没起作用，改成下面的配置：
```
<Directory />
#AllowOverride none
#Require all denied
Options FollowSymLinks
AllowOverride None
Order deny,allow
Deny from all
</Directory>
```
ps:

“Options All”是允许目录浏览，有安全性风险，适合用于个人调试程序，需注意当站点根目录含index.html页面时，会默认打开网页，而不是目录列表，因此此模式需删除index.html.
“Options Indexes FollowSymLinks ExecCGI Includes”是不允许目录浏览，适合正式站点

 

4.打开文件httpd-vhosts.conf文件，目录是/Applications/XAMPP/xamppfiles/etc/extra/httpd-vhosts.conf


```
<VirtualHost *:80>
    ServerAdmin webmaster@dummy-host.example.com
    DocumentRoot "/Applications/XAMPP/xamppfiles/docs/dummy-host.example.com"
    ServerName dummy-host.example.com
    ServerAlias www.dummy-host.example.com
    ErrorLog "logs/dummy-host.example.com-error_log"
    CustomLog "logs/dummy-host.example.com-access_log" common
</VirtualHost>

<VirtualHost *:80>
    ServerAdmin webmaster@dummy-host2.example.com
    DocumentRoot "/Applications/XAMPP/xamppfiles/docs/dummy-host2.example.com"
    ServerName dummy-host2.example.com
    ErrorLog "logs/dummy-host2.example.com-error_log"
    CustomLog "logs/dummy-host2.example.com-access_log" common
</VirtualHost>


<VirtualHost *:80>
    ServerAdmin mofeng@server.com
    DocumentRoot "/Users/xxx/www"
    ServerName mofeng.server.com
    ServerAlias www.server.taobao.com
    ErrorLog "logs/dummy-host.mofeng.com-error_log"
    CustomLog "logs/dummy-host.mofeng.com-access_log" common
</VirtualHost>
```

参考：http://www.cnblogs.com/heiniuhaha/archive/2011/10/14/2212478.html
