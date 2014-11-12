---
layout: post
title: stream
description: 
categories: NodeJs
---

几个使用stream的例子：

```
 #!/usr/bin/env node
 var child = require('child_process');
 var myREPL = child.spawn('node');
 myREPL.stdout.pipe(process.stdout, { end: false });
 process.stdin.resume();
 process.stdin.pipe(myREPL.stdin, { end: false });
 myREPL.stdin.on('end', function() {
   process.stdout.write('REPL stream ended.');
 });
 myREPL.on('exit', function (code) {
   process.exit(code);
 });
```


```
#!/usr/bin/env node

 var child = require('child_process'),
     fs = require('fs');

 var myREPL = child.spawn('node'),
     myFile = fs.createWriteStream('myOutput.txt');
 myREPL.stdout.pipe(process.stdout, { end: false });
 myREPL.stdout.pipe(myFile);
 process.stdin.resume();
 process.stdin.pipe(myREPL.stdin, { end: false });
 process.stdin.pipe(myFile);

 myREPL.stdin.on("end", function() {
   process.stdout.write("REPL stream ended.");
 });

 myREPL.on('exit', function (code) {
   process.exit(code);
 });
```

一个简单的代理实现：

```
#!/usr/bin/env node

 var http = require('http');

 http.createServer(function(request, response) {
   var proxy = http.createClient(9000, 'localhost')
   var proxyRequest = proxy.request(request.method, request.url, request.headers);
   proxyRequest.on('response', function (proxyResponse) {
     proxyResponse.pipe(response);
   });
   request.pipe(proxyRequest);
 }).listen(8080);

 http.createServer(function (req, res) {
   res.writeHead(200, { 'Content-Type': 'text/plain' });
   res.write('request successfully proxied to port 9000!' + '\n' + JSON.stringify(req.headers, true, 2));
   res.end();
 }).listen(9000);
```
