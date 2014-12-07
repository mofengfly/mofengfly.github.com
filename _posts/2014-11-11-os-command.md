---
layout: post
title: linux上一些常用的命令
description: 
categories: OS
---

* GET请求：curl -i -X GET http://www.baidu.com
* POST：curl -i -X POST -H 'Content-type:application/json' -d '{"name": "andy"}' http://localhost:8080
* 获取进程id: pgrep -n -f 'node'
* 批量杀死进程1：kill $(ps ax | grep '[j]s' | awk '{ print $1 }') 
* 批量杀死进程2：lsof -i tcp:22 | grep LISTEN | awk '{print $2}' | xargs echo kill


* 查看端口：netstat -apn|grep 80
