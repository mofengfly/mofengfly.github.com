---
layout: post
title: [译]转换, toString 和 valueOf
categories: javascript
---
原文链接： [Conversion, toString and valueOf](http://javascript.info/tutorial/object-conversion)

JS中的对象在3种环境下可以转换成原始值(primitives)。

 1. 数字(Numeric)
 2. 字符串(String)
 3. 布尔值(Boolean)

理解转换工作的方式有助于规避可能的陷阱，写出更干净的代码。

###字符串转换

当需要对象的字符串表示时，字符串转换就会发生。

例如：

```
var obj = { name: 'John' }

alert(obj) // [object Object]

```
也可以使用String(obj)来进行显示的转换

####对象到字符串的转换的算法

1. 如果存在toString并且返回原始值，就返回它。一般执行都会在这儿停止，以为toString方法在所有对象上都存在
2. 如果存在vauleOf并且返回原始值，就返回它.
3. 否则的话，抛出异常

再强调一下，一般所有对象都会有toString方法。内置的对象会有自己的toString实现。

```
alert( {key: 'value'} ) // toString for Objects outputs: [object Object]
alert( [1,2] )          // toString for Arrays lists elements "1,2" 
alert( new Date )       // toString for Dates outputs the date as a string

```
####定制toString

我们可以为自己创建的对象定制toString方法。

```
var user = {

  firstName: 'John',

  toString: function() {
    return 'User ' + this.firstName 
  }
}

alert( user )  // User John

```

###数值转换

JS中还有一类准换不像toString那么为大家所熟知，但是在内部使用的非常频繁。

数值转换的发生场景主要由2中情况：

- 在需要数字的函数里，例如 Math.sin(obj),isNaN(obj),包括算术操作符+obj
- 在比较的时候，比如obj == 'john'.有个例外是字符串比较===，它不会做任何类型转换。

显示转换可以使用Number(obj)来完成。

数值转换的算法

2. 如果存在vauleOf并且返回原始值，就返回它.
3. 否则的话，如果存在toString并且返回原始值，就返回它.
3. 否则的话，抛出异常

内置对象里，Date同时支持数值和字符串准换

```
alert( new Date() ) // The date in human-readable form
alert( +new Date() ) // Microseconds till 1 Jan 1970

```

####定制valueOf方法

和toString一样，valueOf也可以被定制：

```
var room = { 

  num: 777,

  valueOf: function() {
    return this.num
  }
}

alert( +room )  // 777

```

如果有一个定制的toString方法，却没有valueOf方法，解释器会使用toSTring来进行数值转换

```

var room = { 

  num: 777,

  toString: function() {
    return this.num
  }
}

alert( room / 3 )  // 259

```
注意：数值转换并不意味着就要返回数字。它必须要返回一个原始值(primitive)，但对于具体的类型没有限制。

因此，把对象转换成字符串的比较好的做法是使用二进制加法。

```

var arr = [1,2,3]

alert( arr + '' ) 
// first tries arr.valueOf(), but arrays have no valueOf
// so arr.toString() is called and returns a list of elements: '1,2,3'

```

由于历史原因，即使Date对象有valueOf方法，`new Date + ''`也返回的是Date的字符串表示，这是一个例外。

其它数学函数不仅会做数值转换，还会强制转成数字，例如单元加号运算符+arr会返回NaN.

```
alert( +[1,2,3] ) // [1,2,3] -> '1,2,3' -> not a number

```

在相等或比较测试中的转换

非严格的相等和比较测试，使用数值比较

只有在对象和原始值比较的时候，"=="才会对对象进行转换。

if (obj == true) { ... }

两个对象的相等检查`obj1 == obj2`不会发生转换，只有在指向同样的引用时，才会返回true.

比较总是会转换到原始值

```
var a = { 
  valueOf: function() { return  1 }
}
var b  = { 
  valueOf: function() { return  0 }
}

alert( a > b )  // 1 > 0, true
```
为什么下面的语句为真

```
alert( ['x'] == 'x' )

```

