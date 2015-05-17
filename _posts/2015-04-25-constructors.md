---

layout: post

title: 构造器 

description: 构造器

categories: javascript

---




近来在看一些javascript的基础知识，JS的基于原型的继承，对于JS基于原型的继承，看过很多遍，但似乎都是死记硬背的，没有
从语言的设计层面，以及基于原型的继承带来的与其他面向对象语言的不同去理解。我需要回归，需要再次从头开始去理解这些概念。

在刚开始学Javascript的class的时候，绕不开构造器，各种教材博客上介绍的JS继承会不断的去围绕构造器做各种的继承。
比如，我们平时很常见的一种创建class的方式：
```
function Person(name) {
  this.name = name || 'nobody';
}
Person.prototype.hello = function hello() {
  return 'Hello, my name is ' + this.name;
}
var p = new Person('George');
```
我自己在写代码的时候，经常用这种模式，基本上写class已经离不开constructor了，但发现，很多大神其实不太赞成使用constructor，
Eric Elliott任务应该停止使用constructor，在[stop-using-constructor-functions-in-javascript/](http://ericleads.com/2012/09/stop-using-constructor-functions-in-javascript/),

他认为用构造器来新建对象违反了open/closed原则，使得构造器的实现和调用代码相互耦合，并且使得实现多态更难。对这一点，他举了一个例子，如果要实现
一个有视频播放器，可以用H5或者flash实现。如果用工厂模式来实现，很容易根据浏览器的支持情况返回相应的播放器类型，而且增加新的类型，对于工厂莫斯来说也非常简单，但是如果使用构造器和this，为了增加新类型就需要修改构造器的代码。另一方面，“new”的使用会违反开闭原则，new 和构造器紧密耦合在一起，如果要替换，需要每个地方都修改。对象的新建可以这样实现：

```
var proto = {
  hello: function hello() {
    return 'Hello, my name is ' + this.name;
  }
};
 
var george = Object.create(proto);
george.name = 'George';

```

这种模式的问题在于，不能在原型对象里存储状态，如果我们修改原型里作为状态的数组或对象，就会影响所有实例，因此我们需要在每个实例里复制一份状态

Douglas Crockford大神在[Prototypal Inheritance in JavaScript](http://javascript.crockford.com/prototypal.html)也建议使用：

```
if (typeof Object.create !== 'function') {
    Object.create = function (o) {
        function F() {}
        F.prototype = o;
        return new F();
    };
}
newObject = Object.create(oldObject);
```

但是Axel Rauschmayer确持有不同的意见，对于ECMAScript 5,他在[In defense of JavaScript’s constructors
](http://www.2ality.com/2013/07/defending-constructors.html)建议：
* 尽量使用构造器
* 在创建对象的时候，尽量使用new

理由是：
* 代码会比较符合主流，容易在不同的框架之间移值
* 速度：在现代的js引擎中，使用构造器新建对象会很快
* ES6中默认的继承行为是基于构造器的

在new一个对象时防止忘记“new”，可以在代码里防范：

```
function Foo(arg) {
        if (!(this instanceof Foo)) {
            return new Foo(arg);
        }
        this.arg = arg;
    }
```

也可以通过strict模式来保护
```
  function Color2(name) {
        'use strict';
        this.name = name;
    }
```
参考：

 * [Fluent JavaScript – Three Different Kinds Of Prototypal OO](http://ericleads.com/2013/02/fluent-javascript-three-different-kinds-of-prototypal-oo/)
 * [JavaScript Constructor Functions Vs Factory Functions](http://ericleads.com/2013/01/javascript-constructor-functions-vs-factory-functions/)
