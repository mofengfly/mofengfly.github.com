---
layout: post
title: 深入理解定时器
description: setTimeout and setInterval 在js经常用另外完成动画，将同步执行的代码转换成异步。这篇文章主要对定时器的内部机制进行分析和介绍关键字：setTimeout and setInterval javascript
categories: javascript
---
浏览器内部是事件驱动的。大多数行为都是异步发生的。浏览器会创建一个事件放入事件队列里。

在时间允许的时候，事件会从队列里取出。例如：

*  脚本完成加载
* Keypress, mousemove.
* resize

许多事件与Javascript整合，许多事件被严格限制在内部。

## Javascript是单线程的

每个window只有一个js线程。像渲染，下载等活动被不同的先出使用不同的优先级管理。

## 异步事件

当异步事件发生的时候，它会进入事件队列。

浏览器有内部的事件循环，它会检查队列和进程事件，执行函数等等。

如果浏览器繁忙的话，setTimeout/setInterval现在也会把函数的执行放入事件队列里。

### Stacked events


     有可能同时会有多个事件加入到事件队列里。


例如，在屏幕同样位置，mouseup事件后面跟着的mousedown会产生点击事件。同时会有2个事件，，moueup和click事件添加到队列里。

focus事件后面可能跟着mousedown。

### setTimeout(func, 0) 的小秘密


如果setTimeout的最后一个参数是0，它会试图尽快的执行函数。


函数的执行在最近的定时器tick进入事件队列。注意，那不是立即执行的。知道下一个tick才会执行。 

###在父节点工作之后延迟触发


事件首先在孩子节点上触发然后冒泡到他的父节点。但是孩子节点可能想要在父节点后触发。


例如，document.keypress来管理hotkeys，然后孩子节点想在hotkey完成之后处理事件。

或者，有一个document.mouse管理的drag and drop，孩子元素想在拖拽后处理它的执行。

事件捕获在IE9以下不被支持。有其它的原因来避免它。
解决方案通常是setTimeout(.., 0)。在以下的例子，click首先被document.body处理，然后才是input:

     <input type="button" value='click'>
     <script>
     var input = document.getElementsByTagName('input')[0]
     
    input.onclick = function() {
      setTimeout(function() {
        input.value +=' input' 
      }, 0)
    }
     
    document.body.onclick = function() {
      input.value += ' body'
    }
    </script>

### 给浏览器一点时间来完成工作


大多数时候，我们可以在浏览器默认行为之前处理事件。但有时候我们需要浏览器行为的结果来处理。

例如，我们把一个输入框的文本转换成大写。


     <input id='my' type="text">
     <script>
         document.getElementById('my').onkeypress = function(event) {
            this.value = this.value.toUpperCase()
         }
     </script>
    


试一下，你会发现，除了最后一个字母，输入框的值都被转换成大写了，因为浏览器在‘keypress’之后才会把字母放到输入框中。当然，我们可以使用‘keyup’。但是字符会作为小写字母显示，然后再key释放的时候才会被变成大写，这看起来会很奇怪。解决方案是使用‘keypress’，但是在timeout里应用准换。

     <input id='my-ok' type="text">
     <script>
       document.getElementById('my-ok').onkeypress = function() {
       var self = this
       setTimeout(function() {
         self.value = self.value.toUpperCase()
         }, 0)
       }
    </script>

timeout会在字符添加后执行，但是很快，因此这个延迟是看不见的。
    
## 异步事件


存在不需要使用事件队列的事件。他们被称为同步事件，会立即工作即使是在其他的handler里。

### DOM改变事件是同步的

在下面的例子中，onclick事件处理器改变链接的属性，有一个DOMAttrModified(IE:onpropertychange)监听器。


同步改变事件在点击的时候会立即处理。

     <script>
       var a = document.body.children[0]
       a.onclick = function() {
         alert('in onlick')
         this.setAttribute('href', 'lala')
         alert('out onclick')
         return false
       }
       function onpropchange() {
         alert('onpropchange')
       }
       if (a.addEventListener) { // FF, Opera
         a.addEventListener('DOMAttrModified', onpropchange, false)
       }
       if (a.attachEvent) { // IE 
          a.attachEvent('onpropertychange', onpropchange)
       } 
     </script>


The click processing order:

click事件处理顺序：

1、alert('in onclick') 

2、属性发生改变后，DOM mutation事件立即同步触发onchange，输出alert('onpropchange').

3、onclik其余部分执行，alert('out onclick').
    
### 嵌入的DOM事件是同步的



     有些方法会触发立即执行的事件，比如elem.focus().这些事件会以同步的方式执行。


运行下面的例子。注意onfocus没有等到onclick完成，它是同步执行的。

     <input type="text">
     <script>
       var button = document.body.children[0]
       var text = document.body.children[1]
       button.onclick = function() {
         alert('in onclick')
         text.focus()
          alert('out onclick')
       }
        text.onfocus = function() {
         alert('onfocus') 
         text.onfocus = null  //(*)
       }
     </script>


上面的例子中，alert的顺序是onclick->focus->out，很清楚的演示了同步的行为。

如果事件是有JS的dispatchEvent/fireEvent触发的，它们也是同步执行的。


同步事件破坏了这种一对一的规则，可能会产生副作用。

例如，onfoucs的处理器可能认为onclick已经完成了工作。

有两种方式来fix:

1、把text.focus() 放到onclick代码的最后。

2、把text.focus() 放入 setTimeout(.., 0)。

      button.onclick = function() {
        alert(1)
        setTimeout(function() { text.focus() }, 0)
        alert(2)
      }


##Javascript执行和渲染


在大多数浏览器中，渲染和JS使用一个事件队列。这意味着，在JS运行的时候，没有渲染发生。


试试下面的demo。你会发现，浏览器可能会停止一些时间，因为它改变div.style.backgroundColor从#A00000 到 #FFFFFF.


在大多数浏览器中，在脚本完成之前，你什么都不看到，或是浏览器停止，并提示“脚本运行时间太长”

      <div style="width:200px;height:50px;background-color:#A00000"></div>
      <input type="button" onclick="run()" value="run()">
      <script>
      function run() {
         var div = document.getElementsByTagName('div')[0]
         for(var i=0xA00000;i<0xFFFFFF;i++) {
           div.style.backgroundColor = '#'+i.toString(16)
         }
      }
      </script>

在Opera里，你或许注意中div被绘制了。但是不是每一次变化会产生一个重绘，或许是因为Opera内部的调度。在这个浏览器里，渲染和JS的事件队列是不同的


在其它浏览器里，渲染会被推迟知道JS执行完毕


实现可能有所不同，但是一般俩说，节点被标记为“dirty”（需要重新计算和重绘），重绘事件排入队列，或者浏览器可能只是在每个脚本完成之后寻找dirty节点，然后处理他们。

      Immediate reflow
      
      浏览器包含了很多优化来加速渲染和绘制。一般来说，它会尽量延迟它们知道脚本执行完成。但是一些行为会要求节点理解重新渲染
      例如
      elem.innerHTML = 'new content'
      alert(elem.offsetHeight)  // <-- rerenders elem to get offsetHeight
      在上面的情况下，浏览器必须执行relayouting来得到高度。但是它不必重新在评论上绘制
      有时候，其它一些独立的节点也会涉及到计算中。这些过程被称为reflow，如果脚本很频繁的触发，可能会占用许多资源。

##setInterval真实的延迟




setInterval(func, delay) 不会保证在执行间的间隔时间。真实的延迟可能快或慢与给定的延迟

事实上，它一点都不能保证会有任何的延迟

如果我们需要有固定的延迟间隔，使用setTimeout.

###最小的延迟


定时器的分辨率是有限的。实际上，现代浏览器的最小定时器tick在1ms和15ms之间，可能比老浏览器更大一些。


如果定时器分辨率是10ms，那么setTimeout(..,1) 和 setTimeout(..,9)就是没有区别的。


我们看看下一个例子。这个例子会运行好几个定时器，从2ms到20ms。每个定时器增加相应div的长度。


在不同的浏览器下运行，注意到对于大多数，前几个div动画效果是一样的。那正是引文定时器在时间很小的情况下没有什么不同。

## 上下文环境中的this

setInterval/setTimeout执行的函数中的this=window，在ES5严格模式下this=undefined;

      <input type="button" 
        onclick="setTimeout(function() { this.value='OK' }, 100)"
        value="Click me"
      >

### 方法调用

在面向对象的代码里，调用的函数可能不会像想象中的那样工作。

     function User(login) {
       this.login = login
       this.sayHi = function() {
         alert(this.login)
       }
     }
     var user = new User('John')
     setTimeout(user.sayHi, 1000)

user.sayHi 确实执行了，但是setTimeout在没有上下文环境中执行函数。


它其实和下面是一样的：

     setTimeout(user.sayHi, 1000) 
     var f = user.sayHi
     setTimeout(f, 1000)

有两种方式解决这个问题，一种方式是创建一个中间函数：

     function User(name) {
       this.name = name
       this.sayHi = function() {
         alert(this.name)
       }
     }
     var user = new User('John')
     setTimeout(function() {
       user.sayHi()
     }, 1000)

第二种方式是通过闭包而不是this来绑定sayHI到对象。

     function User(name) {
       this.name = name
       var self = this
       this.sayHi = function() {
        alert(self.name)
       }
     }
     var user = new User('John')
     setTimeout(user.sayHi, 1000)




#总结


大多数浏览器共用UI和JS线程，会被同步调用阻塞。因此，JS执行阻塞渲染。


除了DOM事件之外，事件都是同步处理的。


setTimeout(..,0)非常有用：

  让浏览器渲染当前的变化
  
  避免 “script is running too long”警告
  
  改变执行流


对于timeout和线程，Operar在许多方面都很特殊


####参考文献

Understanding timers: setTimeout and setInterval：http://javascript.info/tutorial/settimeout-setinterval

Events and timing in-depth： http://javascript.info/tutorial/events-and-timing-depth
