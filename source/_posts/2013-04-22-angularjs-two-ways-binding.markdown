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
它每次跳动的时候会触发所属的scope和其所有子scope的dirty checking，dirty checking又会触发$watch()（马上会介绍`$watch()`），整个Angular双向绑定机制就活了起来，页面也会随之更新~   
注意：不建议直接调用`$scope.$digest()`，而应该使用`$scope.$apply()`，原因一会儿细说~   
**（曾经在这里写过Digest是每50ms“跳动”一次，网上很多文章也是这么写的，其实这样的说法并不全面严谨，原因在这个文章后面会细细说明~）**

# $watch()

{% img /my-images/angularjs-2-ways-1-2.png %}

每个成功的digest背后都有一群好watch~

- 在digest执行时，如果`watch`观察的value与上次执行时不一样时，就会被触发
- AngularJS内部的watch实现了页面随model的及时更新，其实我们每创建一个model，比如`{{nameModel}}`，AngularJS都会在后台悄悄的为这个model创建一个watch去监听它的变化
- 也可手动调用~   
	**参数1**：待观察的value   
	**参数2**：value改变时想执行的操作，两个参数分别是改变前后的值   
	**参数3**：默认是`false`，使用的是JavaScript本身提供的比较方式，`true`表示比较的是真实的值，会有这个区别是由于JavaScript里对对象的比较是比较的引用地址（可参考[这篇blog](/blog/javascript-variable-assignment/ "[JS] 让人犯晕的Javascript变量赋值")），所以如果watch的是一个对象类型的数据，即使重新赋值了相同的内容，也会触发change事件，比如watch的变量对应的值是一个数组`[1, 2]`，如果再次给这个变量赋值`[1, 2]`，是会触发`watch`里面的参数2函数的，而大部分时候，对于相同的内容，我们不希望执行`watch`里的操作，所以可以把第三个参数设置成true，这个时候就会调用`angular.equals`来进行比较，`angular.equals`是会比较对象里每一个属性的值是否一样的。当然，如果watch的是五种基本类型（Undefined, Null, Boolean, Number和String）就不需要设置了，因为它们不会发生这种值相同却不相等的情况。

{% codeblock lang:js %}
$scope.$watch('name', function(newValue, oldValue) { 
    /* Do something here */ 
}, true);
{% endcodeblock %}

# $apply()

{% img /my-images/angularjs-2-ways-1-3.png %}

我们可以把`apply`看成个给AngularJS送信的~   
`$scope.$apply()`会触发digest，如果有一个function参数，function会先被执行，再digest~

**应该啥时候自己调用呢？**   
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

jQuery对blur事件的绑定就属于AngularJS机制外触发，必须使用`$apply`才能生效。（新版本的AngularJS已经提供`ng-blur`这个directive了，这里作为例子看一下就好~）

# 再细细说一下`$digest`和`&apply`

通过刚才的描述，我们已经知道了Angular内部会自动为页面显示的model创建watcher，然后我们也可以自定义一些watcher去监听model，然后在model变化时做一些自己想做的事情~   
然后`$watch()`是由谁来触发的呢？就是`$digest()`【恭喜我自己都会抢答了。。。】   
那么问题来了，咳咳，**$digest()是由谁触发的呢？**   
其实有两种角色：

1. angular提供的directive之类的，比如你用到了angular的`ng-click`这个directive，在它对应的函数或表达式里你改变了某个model的值，这时候Angular觉察到“我擦不好有变动~”，就会自动触发一个`$scope.$apply()`，而这个apply又会调用`$rootScope.$digest()`，于是一轮由顶至下的dirty checking就轰轰烈烈的荡漾开了~页面也会随之更新了~   
2. Angular体制外对model修改后手动调用了`$apply`，也会调用`$rootScope.$digest()`，刚刚介绍过~

然后问题又来了，**就这么一轮dirty checking也好意思叫自己心跳？**    
-_-确实勉强了点儿，可是就一轮儿多省事啊，为啥不行呢，回想一下`$watch`方法，我们在监听model改变的时候可以传入一个回调函数，如果我们在这个回调函数里又改变了其它model的值怎么办。。。不怕，Angular也想到了，所以它会一遍一遍的由顶至下的dirty checking，直到所有的model都没有改动了，至少为两轮，但是这样也有问题啊，万一有什么死循环或者过多的互相修改，这性能多差啊，得循环到啥时候去啊，没完没了了怎么办？没关系！Angular也想到了，所以设置了一个默认值为10的TTL（Time to Live），简单的说就是我就给你循环十次，即使循环到第十次model还有不一样，爷也不陪你玩儿了~

# Performance
下面开始说性能了，动不动就循环个2到10次，受不受得了呢？

AngularJS的创建者曾经在stackoverflow上回过一篇巨火的[答复](http://stackoverflow.com/questions/9682092/databinding-in-angularjs?answertab=votes#tab-top)，里面提到了他做了一个实验，他在一个页面搞了10,000个watcher，在流行的浏览器里dirty checking用了不到6ms，而巨慢的IE8也只用了40ms，他也列出了下面的科学研究结果：

- **人对变化的反应是慢的**：任何比50ms还快的变化都是不可被察觉的~
- **人对信息的处理能力是有限的**：在一页处理2000条信息已经算是极限了，再多的信息量往一页上堆只能说是不好的设计了，而且人也无法处理了~

所以问题就演变为：**我们能不能在50ms里做2000次比较呢？**   
以现在的技术来说，即使是很慢的浏览器也没问题的。当然如果每个比较都写的特别复杂就另说了~而且在watch里过多的去改变其它的model值也绝不是个好习惯啊，所以当遇到这种情况的时候，就是一种代码的坏味道了，看看是不是可以重构简化一下啦~
