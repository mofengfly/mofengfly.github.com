---
layout: post
title: node stream
categories: nodejs
---
https://github.com/substack/stream-handbook

node的stream是个非常强大的工具，可以让我们的程序变得简洁，优雅，可组装的，一下就高大上了。

例如，我们读取文件时，可能会这样做：
    var http = require('http');
    var fs = require('fs');

    var server = http.createServer(function (req, res) {
        fs.readFile(__dirname + '/data.txt', function (err, data) {
            res.end(data);
        });
    });
    server.listen(8000);
    
对于每次请求，在把结果写回到客户端之前，需要把data.txt在内存中缓存。如果data.txt很大的话，就会耗用大量内存。这样的用户体验当然非常差了，因为用户需要等待你把整个文件在服务器上缓存才能开始接受返回的内容。

幸好，res和req都是stream，意味着我们可以使用fs.createReadStream来让代码更优雅。



    var http = require('http');
    var fs = require('fs');

    var server = http.createServer(function (req, res) {
        var stream = fs.createReadStream(__dirname + '/data.txt');
        stream.pipe(res);
    });
    server.listen(8000);
    
`.pipe()`会自己监听处理来自fs.createReadStream的`data`和`end`事件，代码很清晰，而且服务端一接收到data.txt文件的数据，都会立即写到客户端。


有5种类型的流：readable, writable, transform, duplex, and "classic"。

###pipe

pipe需要哦一个可读流src，然后将其输出到可写流dst.

    src.pipe(dst)

pipe(dst)返回可写流，因此可以：

a.pipe(b).pipe(c).pipe(d)

###readable streams

可读流生成数据通过调用.pipe可以输入到writable, transform, or duplex stream

####creating a readable stream

    var Readable = require('stream').Readable;

    var rs = new Readable;
    rs.push('beep ');
    rs.push('boop\n');
    rs.push(null);

    rs.pipe(process.stdout);


通过定义._read函数我们可以按需推送数据块

    var Readable = require('stream').Readable;
    var rs = Readable();

    var c = 97;
    rs._read = function () {
        rs.push(String.fromCharCode(c++));
        if (c > 'z'.charCodeAt(0)) rs.push(null);
    };

    rs.pipe(process.stdout);

这个例子只有在consumer请求的时候才会推送数据。修改一下代码，增加一点延迟，可以显示只有在consumer请求的时候，_read方法才会被调用。

    var Readable = require('stream').Readable;
    var rs = Readable();

    var c = 97 - 1;

    rs._read = function () {
        if (c >= 'z'.charCodeAt(0)) return rs.push(null);

        setTimeout(function () {
            rs.push(String.fromCharCode(++c));
        }, 100);
    };

    rs.pipe(process.stdout);

    process.on('exit', function () {
        console.error('\n_read() called ' + (c - 97) + ' times');
    });
    process.stdout.on('error', process.exit);


可以用util.inherits() 来继承stream.


 