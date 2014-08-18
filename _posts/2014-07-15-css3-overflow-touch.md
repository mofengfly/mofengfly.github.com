---
layout: post
title: [转]SSE：服务器发送事件
categories: web
---
##1 概述
        
传统的网页都是浏览器向服务器“查询”数据，但是很多场合，最有效的方式是服务器向浏览器“发送”数据。比如，每当收到新的电子邮件，服务器就向浏览器发送一个“通知”，这要比浏览器按时向服务器查询（polling）更有效率。

服务器发送事件（Server-Sent Events，简称SSE）就是为了解决这个问题，而提出的一种新API，部署在EventSource对象上。目前，除了IE，其他主流浏览器都支持。

简单说，所谓SSE，就是浏览器向服务器发送一个HTTP请求，然后服务器不断单向地向浏览器推送“信息”（message）。这种信息在格式上很简单，就是“信息”加上前缀“data: ”，然后以“\n\n”结尾。

    $ curl http://example.com/dates
    data: 1394572346452

    data: 1394572347457

    data: 1394572348463

    ^C
SSE与WebSocket有相似功能，都是用来建立浏览器与服务器之间的通信渠道。两者的区别在于：

    》 WebSocket是全双工通道，可以双向通信，功能更强；SSE是单向通道，只能服务器向浏览器端发送。

    》WebSocket是一个新的协议，需要服务器端支持；SSE则是部署在HTTP协议之上的，现有的服务器软件都支持。

    》SSE是一个轻量级协议，相对简单；WebSocket是一种较重的协议，相对复杂。

    》SSE默认支持断线重连，WebSocket则需要额外部署。

    》SSE支持自定义发送的数据类型。
从上面的比较可以看出，两者各有特点，适合不同的场合。

##2.客户端代码

###2.1 概述

首先，使用下面的代码，检测浏览器是否支持SSE。

    if (!!window.EventSource) {
      // ...
    }
    
然后，部署SSE大概如下。   

    var source = new EventSource('/dates');

    source.onmessage = function(e){
      console.log(e.data);
    };

// 或者

source.addEventListener('message', function(e){})
参考：

http://javascript.ruanyifeng.com/htmlapi/eventsource.html


