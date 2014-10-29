---
layout: post
title: 数据绑定：Object.observe()
description: 
categories: Chrome 36已经实现了Object.observe()，ECMAScript的未来标准，以后完成数据绑定在也不用引入第三方库了。
---

Object.observe()是异步观察JS对象变化的方法，它可以让观察者按时间顺序接受到发生在被观察的对象属性上得变化。

例如：

```
// Let's say we have a model with data
var model = {};

// Which we then observe
Object.observe(model, function(changes){

    // This asynchronous callback runs
    changes.forEach(function(change) {

        // Letting us know what changed
        console.log(change.type, change.name, change.oldValue);
    });

});
```

有了Object.observe()，我们就可以不依赖框架实现双向绑定，而且拥有更好的性能。

可以观察 ：
* 原始的JS对象的变化
* 属性何时增加，修改和删除
* 发生在对象原型上的变化


参看：http://www.html5rocks.com/en/tutorials/es7/observe/?redirect_from_locale=zh
