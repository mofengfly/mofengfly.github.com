---
layout: post
title: nodejs pm2用到的npm模块
categories: NodeJS
---

这几天再看PM2的源码，发现了很多没见过的NPM模块，很好很强大，记录一下，以后能用到。

###Commander
[Commander.js](http://visionmedia.github.io/commander.js)是Ruby中Commander在node.js中的实现,提供了用户命令行输入和参数解析强大功能。

###[chalk](https://github.com/sindresorhus/chalk)

[chalk](https://github.com/sindresorhus/chalk)可以在命令行环境下对字符串设置样式，比如设置字符在命令行里显示的颜色、粗体，斜体等等

###[axon](https://github.com/visionmedia/axon)

[axon](https://github.com/visionmedia/axon)基于消息的socket库。它的主要优点：

* 基于消息
* 自动重新连接
* 轻量级的网络协议
* 复合类型的参数（strings, objects, buffers等）
* unix域套接字支持（unix domain socket support）
* 快速(~800 mb/s ~500,000 messages/s)

####模式

* push / pull
* pub / sub
* req / rep
* pub-emitter / sub-emitter

注：在window下无法用

###[json-stringify-safe](https://github.com/isaacs/json-stringify-safe)

[json-stringify-safe](https://github.com/isaacs/json-stringify-safe)功能和JSON.stringify一样，但是在转换有循环引用的对象是不会报错。


###[EventEmitter2](https://github.com/asyncly/EventEmitter2)

[EventEmitter2](https://github.com/asyncly/EventEmitter2)

参见：http://dailyjs.com/2011/09/19/code-review/

