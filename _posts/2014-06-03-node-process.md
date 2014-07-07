---
layout: post
title: nodejs 多进程管理
categories: NodeJS
---

这几天再看PM2的源码，看了一堆的fork，cluster，头大，不明所以，老老实实把node的进程管理这一块儿的东东整理学习一下。

##ChildProcess

子进程总是会有3个相关的流：child.stdin、chidl.stdout和child.stderr。这些可能会与父进程共享或者独立的流对象

###child_process.spawn

调用形式child_process.spawn(command, [args], [options])，启动一个新的进程来只执行命令，其中：

command: 字符串，代表要执行的命令

args：数组，命令执行时的参数

options：对象
* cwd: 字符串，子进程的当前工作目录
* stdid: 字符串或数组，子进程stdio的配置
* env: 对象，环境键值对
* detached: 布尔类型，The child will be a process group leader. 
* uid:Number,设置进程的用户身份
* gid: Number,设置进程的组身份

返回子进程对象

    var spawn = require('child_process').spawn,
    ls    = spawn('ls', ['-lh', '/usr']);

    ls.stdout.on('data', function (data) {
      console.log('stdout: ' + data);
    });

    ls.stderr.on('data', function (data) {
      console.log('stderr: ' + data);
    });

    ls.on('close', function (code) {
      console.log('child process exited with code ' + code);
	});
    
'stdio'的值是一个数组，数组中的每一个索引都对应子进程中的一个fd，取值如下：

* “pipe”: 在父子进程之间创建一个管道。管道的父端在child_process中会作为一个属性prarent（ ChildProcess.stdio[fd]）存在。为fd0-2创建的管道也可以相应的通过ChildProcess.stdin, ChildProcess.stdout and ChildProcess.stderr获得。
* 'ipc' ：在父子进程之间创建一个IPC通道用来传递消息或者文件描述符。一个子进程最多可以有一个IPC stdio文件描述符。设置这个选项可以是ChildProcess.send()可用。如果子进程向文件描述符希尔JSON消息，就就触发ChildProcess.on('message')。如果子进程是一个NodeJs程序，那么IPC通道的存在就会启用process.send()和process.on(message)
* 'ignore': 不在子进程中设置这个文件描述符。注意这个节点总是会打开它启动的进程的0-2的文件描述符。在它们中的任何一个被忽略之后，node会打开/dev/null，并把它加到子进程的文件描述上
* Stream object ：共享一个可读或可写的流，这个流指向一个终端或是文件、socket或者与子进程的管道。这个流潜在的文件描述符在子进程里被复制到对stdio数组对应的索引的文件描述符。注意stream必须有一个潜在的描述符（文件流只有在‘open’事件打开时才能使用）
* 正整数：整数值表示在当前父进程中打开的一个文件描述符。它与子进程共享，和流对象的共享类似
* null, undefined ：缺省值。对于管道创建的stdio的描述符0,1,2(对应stdin,stdout,和stderr)。对于3及其以上，默认值是'ignore'

ignore - ['ignore', 'ignore', 'ignore']
pipe - ['pipe', 'pipe', 'pipe']
inherit - [process.stdin, process.stdout, process.stderr] or [0,1,2]

默认情况下，父进程会等子进程退出。为了避免父进程一致等待，可以使用方法child.unref()，父进程的时间循环在引用计数中就不会包括子进程了。

    var fs = require('fs'),
         spawn = require('child_process').spawn,
         out = fs.openSync('./out.log', 'a'),
         err = fs.openSync('./out.log', 'a');

     var child = spawn('prg', [], {
       detached: true,
       stdio: [ 'ignore', out, err ]
     });

     child.unref();

当使用detached选线启动一个一直运行的进程时，进程不会一致在背景运行触发它提供了一个没有连接到父母stdio配置。如果父进程的stdio是inherited，子进程会仍然附加到控制终端。


###child_process.exec

调用形式child_process.exec(command, [options], callback)

command: 字符串，要运行的命令，参数用空格分开

options： 对象

* cwd: 字符串，子进程的当前工作目录
* stdid: 字符串或数组，子进程stdio的配置
* env: 对象，环境键值对
* encoding: 字符串(默认是“UTF-8”)
* timeout: Number,默认是0
* maxBuffer Number (默认大小是200*1024)
* killSignal 字符串(Default: 'SIGTERM')

当进程终止的时候会使用output调用回调函数
* error Errror
* stdout Buffer
* stdin Buffer

返回子进程对象

        var exec = require('child_process').exec,
            child;
        child = exec('cat *.js bad_file | wc -l',
          function (error, stdout, stderr) {
            console.log('stdout: ' + stdout);
            console.log('stderr: ' + stderr);
            if (error !== null) {
              console.log('exec error: ' + error);
            }
        });
       
回调得到参数 (error, stdout, stderr).一旦成功，error是null.如果有错，error是Error的一个实例。。code是子进程的退出码。error.signale会被这只为终止进程的信号。

如果timeout大于0，如果允许时间超过了timeout ms，会杀死子进程。子进程用killSignal杀死(默认是'SINTERM').maxBuffer设置了再stdout或者stderr允许的最大数据量-如果超过了，子进程会被杀死。

###child_process.execFile

child_process.execFile(file, [args], [options], [callback])

###child_process.fork

child_process.fork(modulePath, [args], [options])

moudlePath: String，在子进程里运行的模块
args: 字符串参数的数组列表
opations: object
* cwd: 字符串，子进程的当前工作目录
* stdid: 字符串或数组，子进程stdio的配置
* env: 对象，环境键值对
* encoding: 字符串(默认是“UTF-8”)
* execPath: String,用于创建子进程的可执行路径
* execArgv: 传递给可执行路径的字符串参数数组列表(默认是process.execArgv)
* silent: Boolean。如果为真，子进程的stdin,stdou和stderr会被输送到父进程，否则的话，他们会从父进程继承，默认是false.
