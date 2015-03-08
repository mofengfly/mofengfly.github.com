---
layout: post
title: Object.create() vs new
description: Object.create()和new都可用来构建新对象，那这2者有何异同呢
categories: javascript
---

Object.create生成的对象直接继承于传递的参数。`Object.create(null)`创建的对象不继承任何对象

使用构造器的new生成的对象继承与构造器的原型。`foo.prototype = null`创建的对象继承自Object.prototype

eg `var o = new foo();` 

o继承foo.prototype
