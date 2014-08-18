---
layout: post
title: 3种不同的Prototypal OO
categories: javascript
---

###委托继承

    function Greeter(name) {
      this.name = name || 'John Doe';
    }

    Greeter.prototype.hello = function hello() {
      return 'Hello, my name is ' + this.name;
    }

    var george = new Greeter('George');

这是我们最常见得的继承方式，可以节约内存，但Eric Elliott反对使用这种构造器的方式来新建对象，他在
[JavaScript Constructor Functions vs Factory Functions](http://ericleads.com/2013/01/javascript-constructor-functions-vs-factory-functions/)和[Stop Using Constructor Functions in JavaScript](http://ericleads.com/2012/09/stop-using-constructor-functions-in-javascript/)说明了原因，他推荐的实现方式是使用工厂模式，如：

var proto = {
  hello: function hello() {
    return 'Hello, my name is ' + this.name;
  }
};
 
var george = Object.create(proto);
george.name = 'George';
    
委托模式最大的问题在于不擅长存储对象的状态。为了保证实例的安全性，需要对对象的状态做一个拷贝。

###克隆继承/Mixins

原型克隆就从一个对象把属性克隆到另外一个属性的过程，不需要维护两个对象之间的引用。

Underscore’s .extend() 和 jQuery’s .extend() 就是这个作用。

    var proto = {
      hello: function hello() {
        return 'Hello, my name is ' + this.name;
      }
    };

    var george = _.extend({}, proto, {name: 'George'});

一般会看到minxin使用这种方式。例如，Backbone用户通过扩展Backbone.Events可以使任何一个对象成为事件触发器，
    var foo = _.extend({
      attrs: {},
      set: function (name, value) {
        this.attrs[name] = value;
        this.trigger('change', {
          name: name,
          value: value
        });
      },
      get: function (name) {
        return this.attrs[name];
      } 
    }, Backbone.Events);

参考：http://www.2ality.com/2013/07/defending-constructors.html