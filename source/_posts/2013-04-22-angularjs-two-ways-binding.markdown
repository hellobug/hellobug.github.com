---
layout: post
title: "[AngularJS系列(3)] View-Model双向绑定背后的故事~"
date: 2013-03-27 22:13
comments: true
categories: [javascript, angularjs]
---

剧情开始之前，先介绍一下重要背景~三个概念~

- **Dirty Checking** – AngularJS内部比较value现在的值和之前的值，如果发生了改变，就触发change事件。
- **Digest** – 执行`Dirty Checking`的机制，由$digest()触发。
- **Apply** – 当dom事件在AngularJS机制外被触发时，需要通知AngularJS进行`Digest`。由`$apply()`触发。

<!-- more -->

# $digest()

{% img /my-images/angularjs-2-ways-1-1.png %}

Digest就像AngularJS的心跳一样~   
它每50ms蹦一次，蹦的时候会触发所属的scope和其所有子scope的dirty checking，dirty checking又会触发$watch()（马上会介绍`$watch()`），整个Angular双向绑定机制就活了起来~（有人可能会问了，蹦的这么频繁，性能体验神马的都没问题么？一会儿咱专门聊这个哈~）
注意：不建议直接调用`$scope.$digest()`，而应该使用`$scope.$apply()`，原因一会儿细说~

# $watch()

{% img /my-images/angularjs-2-ways-1-2.png %}

每个成功的digest背后都有一群好watch~

- 在digest执行时，如果`watch`观察的value与上次执行时不一样时，就会被触发
- AngularJS内部的watch实现了页面随model的及时更新
- 也可手动调用~   
	**参数1**：待观察的value   
	**参数2**：value改变时想执行的操作   
	**参数3**：true表示比较的是值，而不是引用，由于javascript对象比较比较的是引用地址（可参考[这篇blog](/blog/javascript-variable-assignment/ "[JS] 让人犯晕的Javascript变量赋值")），所以即使重新赋值了相同的内容，也会触发change事件，而大部分时候，对于相同的内容，我们不希望执行watch里的操作，所以可以把第三个参数设置成true。

{% codeblock lang:js %}
$scope.$watch('name', function(newValue, oldValue) { 
    /* Do something here */ 
}, true);
{% endcodeblock %}

# $apply()

{% img /my-images/angularjs-2-ways-1-3.png %}

我们可以把`apply`看成个给AngularJS送信的~   
`$scope.$apply()`会触发digest，如果有一个function参数，function会先被执行，再digest~

**应该啥时候用呢？**   
当dom事件在AngularJS机制外被触发时~

**什么样的情况算机制外呢？**  
喂，jQuery，你就别看别人了~！！

现在到这个问题了，**为啥推荐使用`$apply`而不是`$digest`？**    
因为`$apply`其实不能把信直接送给`$digest`，之间还有`$eval`门卫把关，如果`$apply`带的表达式不合法，`$eval`会把错误送交`$exceptionHandler service`，合法才触发digest，所以更安全~

**举个栗子~**

{% codeblock lang:html %}
<input type="text" ng-blur="closeDialog()" />
{% endcodeblock %}

{% codeblock lang:js %}
myApp.directive('ngBlur', function(){
     return {
          restrict:'A',
          link: function (scope, element, attrs) {
               $(elelment).bind('blur', function(){
                    scope.$apply(attrs.ngBlur);
               });
          }
     };
});
{% endcodeblock %}

jQuery对blur事件的绑定就属于AngularJS机制外触发，必须使用`$apply`才能生效。

# Performance
下面开始说性能了，50ms跳一次，受不受得了呢？

**先来看两条科学研究的结果：**

- **人对变化的反应是慢的**：任何比50ms还快的变化都是不可被察觉的~
- **人对信息的处理能力是有限的**：在一页处理2000条信息已经算是极限了，再多的信息量往一页上堆只能说是不好的设计了，而且人也无法处理了~

所以问题就演变为：**我们能不能在50ms里做2000次比较呢？**   
其实，以现在的技术来说，即使是很慢的浏览器也没问题的。当然如果每个比较都写的特别复杂就另说了~也就是说在写watch语句时，如果要watch的语句太复杂的话，看看是不是可以重构简化一下啦~
