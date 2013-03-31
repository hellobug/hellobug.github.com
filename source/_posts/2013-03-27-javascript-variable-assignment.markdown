---
layout: post
title: "[JS] 让人犯晕的Javascript变量赋值"
date: 2013-01-27 23:17
comments: true
categories: [javascript]
---

# 变量赋值 #

开始之前先来几个例子，确保起始点是晕的状态～ :P

{% codeblock lang:js 例1.1 %}
var a = "apple";
var b = a;
a = "banana";
b
{% endcodeblock %}

按理说，b = a后，a是啥值b就应该跟着是啥值了~  
但，`b`结果是`"apple"`，还是一开始赋值时a的值。  

<!-- more -->

{% codeblock lang:js 例1.2 %}
var a = {name: "apple"};
var b = a;
a.name = "banana";
b.name
{% endcodeblock %}

这回，b又不争气的跟着a变了，`b.name`结果是`"banana"`

{% codeblock lang:js 例1.3 %}
var a = {name: "apple"};
var b = a;
a = {name: "banana"};
b.name
{% endcodeblock %}

这回b又坚持自己了，`b.name`结果是`"apple"`

{% codeblock lang:js 例1.4 %}
var a = {count: 2};
var b = a.count;
a.count = 3;
b
{% endcodeblock %}

同样`b`的结果还是最开始的`2`，b到底是要闹哪样？！{% img /my-images/emotions/xianzhuozi.jpg %}

======================开始解释的分割线==============================
  
其实b很无辜，这个要从ECMAScript的变量值类型说起~

类型共有两种：

* **基本类型 (primitive values)** - 包括Undefined, Null, Boolean, Number和String五种基本数据类型
* **引用类型 (reference values)** - 保存在内存中的对象们，不能直接操作，只能通过保存在变量中的地址引用对其进行操作

现在回来看第一个例子`例1.1`

{% codeblock lang:js %}
var a = "apple";
{% endcodeblock %}

`"apple"`是String类型，属于基本类型，这时值是这样储存的：

{% img /my-images/javascript-variable-assignment-1-1.png %}

{% codeblock lang:js %}
var b = a;
{% endcodeblock %}

这时a的值被copy了一份赋给了b：

{% img /my-images/javascript-variable-assignment-1-2.png %}

所以，从此a和b井水不犯河水，各自怎么修改都不会影响对方了~

再来看第二个例子`例1.2`

{% codeblock lang:js %}
var a = {name: "apple"};
{% endcodeblock %}

`{name: "apple"}`是一个Object，属于引用类型，赋值前后值是这样存储的：

{% img /my-images/javascript-variable-assignment-1-3.png %}

所以当`a.name = "banana";`时，修改的是大家共同指向的内存中的object的属性值，所以`b.name`的值也就跟着变了。

`例1.3`中，

{% codeblock lang:js 例1.3 %}
var a = {name: "apple"};
var b = a;
a = {name: "banana"};
// {name: "banana"} 是内存中的一个新的Object了，
// a变量存储的地址也是指向这个新的Object的了，所以和b又无关了
b.name //还是"apple"
{% endcodeblock %}

`例1.4`中，

{% codeblock lang:js 例1.4 %}
var a = {count: 2};
var b = a.count;
// a.count是String类型，所以值被copy给b，
// 从此再怎么修改与b无关了
a.count = 3;
b //还是2
{% endcodeblock %}

**小总结，变量赋值时总是会copy一份的，如果是基本类型，copy的就是实际的值，如果是引用类型，copy的是指向Object的地址值，所以指向的还是同一个Object。**

* * *

# 变量比较 #

顺手再来看看变量的比较~

{% codeblock lang:js 例2.1 %}
var a = "apple";
var b = "apple";
a == b
{% endcodeblock %}

这个没问题，结果肯定是`true`。

那这个呢？

{% codeblock lang:js 例2.2 %}
var a = ["apple"];
var b = ["apple"];
a == b
{% endcodeblock %}

虽然俩数组长一模一样，结果还是`false`。

其实原理还是一样，**对于基本类型，比较的就是实际的值，而对于引用类型（Array也是一种Object），比较的是地址值**，虽然两个数组内容是一样的，但它们在内存中是两个Object，地址是不一样，所以比较的结果是`false`。

