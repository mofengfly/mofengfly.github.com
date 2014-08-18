---
layout: post
title:nodejs webstorm远程调试
categories: nodejs
---

nodejs调试感觉还是挺麻烦的，近日想在win7上用webstom调试在linux的虚拟机上跑的nodejs。需要如下配置：

###node --debug-brk app.js

使用命令 node --debug-brk app.js启动nodejs程序，调试端口默认是5858，监听的是localhost的端口，其它主机是无法访问到这个端口的，因此需要运行一个代理，将其它主机到5858的请求转发到对应的调试端口。可以使用[balance](http://www.inlab.de/balance.html)， balance是一款负载均衡软件，通过对指定IP-Port列表的轮循, 实现对资源的更合理高效利用.

###安装balance

    wget -c http://www.inlab.de/balance-3.54.tar.gz  
    tar zxvfp balance-3.54.tar.gz  
    cd balance-3.54  
    make  
    make install  
    
安装的时候有可能报错, 说是/usr/sbin/../man/man1这个目录找不到, 自己建一下该目录。
安装好后，运行balance -df 8585 127.0.0.1.5858

###在webstorm里设置nodejs remote debug配置，host配置为虚拟机的host

配置完成后就可以打断点进行调试了