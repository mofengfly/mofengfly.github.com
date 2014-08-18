---
layout: post
title: Js中的number类型
categories: javascript
---

有时候，我们需要将某种类型转换成number，一般会有两种方法：

* Number(value)
* +value

例如：

	> Number('')
	0
	> Number('123')
	123
	> Number('\t\v\r12.34\n ')  // ignores leading and trailing whitespace
	12.34

	> Number(false)
	0
	> Number(true)
	1
	
parseFloat()也可以实现转换，但一般来说，Number(value)是更好的方式。

parseFloat(str)会把str转换成字符串，去掉前面的空格，然后转换最长的浮点数前缀，如果没有这样的前缀（比如是空字符串），返回NaN.

parseFloat() and Number()的区别：

* 用parseFloat转换非字符串是无效的，因为它会把参数强制转换成字符串再开始转换。结果，许多Number转换成数字的值会被转换成NaN，例如：

	> parseFloat(true)  // same as parseFloat('true')
	NaN
	> Number(true)
	1

	> parseFloat(null)  // same as parseFloat('null')
	NaN
	> Number(null)
	0
	
* parseFloat() 会把空字符串转换成NaN

	> parseFloat('')
	NaN
	> Number('')
	0
* parseFloat() 转换到最后一个合法的字符串，可能不是你想要的结果。

	> parseFloat('123.45#')
	123.45
	> Number('123.45#')
	NaN
	
* parseFloat()会忽略前面的空格，遇到任何非法字符停止转换，而Number也会忽略前后的空格（其它的非法字符会导致返回NaN）

	> parseFloat('\t\v\r12.34\n ')
	12.34
	
###特殊的number值

* 两个错误值：NaN和Infinity
* 0有2个值：+0和-0


####NaN

	> typeof NaN
	'number'
	
它一般会由下面几种方式产生：

* 不能被转换的数字：
	> Number('xyz')
	NaN
	> Number(undefined)
	NaN

* 运算无法得到结果
	> Math.acos(2)
	NaN
	> Math.log(-1)
	NaN
	> Math.sqrt(-1)
	NaN
	
* 有一个运算数是NaN

	> NaN + 3
	NaN
	> 25 / NaN
	NaN
	
##### 如何判断一个值是否是NaN

NaN是唯一一个不等于自身的值

	> NaN === NaN
	false
	
“===”也被用于Array.prototype.indexOf，因此不能用这个方法在数组中搜索NaN.

	> [ NaN ].indexOf(NaN)
	-1
	
如果要判断一个值是否是NaN，可以使用全局函数isNaN()

	> isNaN(NaN)
	true
	> isNaN(33)
	false
	
但是，isNaN对于非数字判断有点问题，它首先会将参数转换成number，转换的结果可能是NaN，函数就会不正确的返回true/

	> isNaN('xyz')
	true
	
因此，更好的方法是加入类型检查：

	function myIsNaN(value) {
		return typeof value === 'number' && isNaN(value);
	}
	
或者，检查value是否与自身相等：

	function myIsNaN(value) {
		return value !== value;
	}
	
要完成一个值到number或字符串的转换，首先需要转换成任意的原始值，然后再转换成最终的值。ECMAScript 定义了一个内部函数ToPrimitive()（不能访问）来完成一个值到原始值得转换，这个函数签名如下：

ToPrimitive(input, PreferredType?)

PreferredType 是可选的，表明转换的最终类型：或者是string或number,取决于ToPrimitive()会转换成string还是number.

If PreferredType is Number, then you perform the following steps:
If input is primitive, return it (there is nothing more to do).
Otherwise, input is an object. Call input.valueOf(). If the result is primitive, return it.
Otherwise, call input.toString(). If the result is primitive, return it.
Otherwise, throw a TypeError (indicating the failure to convert input to a primitive).
	
如果PreferredType是Number，就会执行如下的步骤：

* 如果input是primitive，直接返回
* 否则，input是一个对象。调用input.valueOf()。如果结果是primitive，返回这个结果
* 否则，调用input.toString()。如果结果是primitive，返回它
* 否则，抛出TypeError.(表示转换失败)

如果PreferredType是String，步骤2和3交互，PreferredType也可以忽略，对于日期类型，它是string，其它类型是Number.这是+和==调用ToPrimitive的方式。

valueOf() 默认返回this,而toString()默认返回类型信息

> var empty = {};
> empty.valueOf() === empty
true
> empty.toString()
'[object Object]'

因此，Number跳过了valueOf(),转换了toString()的结果为字符串，即， 转换'[object Object]' ，结果就是NaN:

	> Number({})
	NaN
下面这例子改写了valueOf，会影响Number(),但不会影响String()
> var n = { valueOf: function () { return 123 } };
> Number(n)
123
> String(n)
'[object Object]'

下面这个例子定制了toString，因为结果可以转换成数字，Number() 可以返回数字。


	> var s = { toString: function () { return '7'; } };
	> String(s)
	'7'
	> Number(s)
	7

参考： http://speakingjs.com/es5/ch11.html