---
layout: post
title: "[AngularJS系列(5-2)] Directive - Compile vs. Link"
date: 2013-08-14 20:35
comments: true
categories: [javascript, angularjs]
---

还是先从栗子们看起~

如果我想实现这样一个功能，当一个input失去光标焦点时(blur)，执行一些语句，比如当输入用户名后，向后台发ajax请求查询用户名是否已经存在，好有及时的页面相应。

输入 **hellobug**     
{% img /my-images/angularjs-directive-2-input-focus.png %}

失去焦点后提示 **hellobug** 这个用户名已经存在    
{% img /my-images/angularjs-directive-2-input-blur.png %}

<!-- more -->

代码如下： 
{% codeblock lang:html HTML %}
<body ng-controller="MainCtrl">
	<lable>用户名: 
	  <input type="text" ng-model="username" ng-blur="checkUsername()" />
	  <span style="color:red;" ng-show="usernameAlreadyExist">用户名已经存在</span>
	</lable>
</body>
{% endcodeblock %}

{% codeblock lang:js controller和directive的定义 %}
app.controller('MainCtrl', function($scope) {
  $scope.checkUsername = function() {
    //send ajax to check on server
    if ($scope.username === 'hellobug') {
      $scope.usernameAlreadyExist = true;
    }
  }
});

app.directive('ngBlur', function($document) {
  return {
    link: function(scope, element, attrs) {
      $(element).bind('blur', function(e){
         scope.$apply(attrs.ngBlur);
      });
    }
  }
})
{% endcodeblock %}

在上面的例子里，directive返回对象里定义的`link`方法在blur事件触发时执行了scope上的`checkUsername()`方法。

如果是只有`link`方法，也可以简单的写成下面这种形式~直接返回`link`对应的function~

{% codeblock lang:js directive的简单写法 %}
app.directive('ngBlur', function($document) {
  return function(scope, element, attrs) {
    $(element).bind('blur', function(e){
       scope.$apply(attrs.ngBlur);
    });
  };
})
{% endcodeblock %}

* * *      

再来这样一个功能，我想让内容为`哈哈哈哈`的dom元素重复n遍，n是自定义的，以达到某种满屏大笑丧心病狂的效果 -_-，我知道`ng-repeat`就已经能干这事儿了，但是如果自己实现一下呢~

{% codeblock lang:html HTML %}
<ul repeater="20">
  <li>哈哈哈哈</li>
</ul>
{% endcodeblock %}

{% codeblock lang:js directive的定义 %}
app.directive('repeater', function($document) {
  return {
    restrict: 'A',
    compile: function(element, attrs) {
      var template = $(element).children().clone();
      for(var i=0; i<attrs.repeater - 1; i++) {
        $(element).append(template.clone());
      }
    }
  }
});
{% endcodeblock %}

在上面例子的`compile`方法里，子元素被复制成了`repeater`制定的数量。

* * *      

什么时候用compile，什么时候用link呢，或者两者可不可以一起用呢？

先从directive是如何在angular手下生效的说起吧~

## 编译三阶段：

### 1. 标准浏览器API转化
将html转化成dom，所以自定义的html标签必须符合html的格式

### 2. Angular compile
搜索匹配directive，按照`priority`排序，并执行directive上的`compile`方法

### 3. Angular link
执行directive上的`link`方法，进行scope绑定及事件绑定

## 为什么编译的过程要分成compile和link?

简单的说就是为了解决性能问题，特别是那种model变化会影响dom结构变化的，而变化的结构还会有新的scope绑定及事件绑定，比如`ng-repeat`

## `compile`和`link`的形式

### compile
- `function compile(element, attrs, transclude) { ... }`
- 在compile阶段要执行的函数，返回的function就是link时要执行的function
- 常用参数为`element`和`attrs`，分别是dom元素和元素上的属性们，其它的以后细说
- 较少使用，因为大部分directive是处理dom元素的行为绑定，而不是改变它们

### link
- `function link(scope, element, attrs, controller) { ... }`
- 在link阶段要执行的函数，这个属性只有当compile属性没有设置时才生效
- 常用参数为`scope`，`element`和`attrs`，分别是当前元素所在的scope，dom元素和元素上的属性们，其它的以后细说
- directive基本上都会有此函数，可以注册事件，并与scope相绑

## `compile`和`link`的使用时机

### compile
- 想在dom渲染前对它进行变形，并且不需要scope参数
- 想在所有相同directive里共享某些方法，这时应该定义在compile里，性能会比较好
- 返回值就是link的function，这时就是共同使用的时候

### link
- 对特定的元素注册事件
- 需要用到scope参数来实现dom元素的一些行为

## 最后~
本节目所用示例可以猛戳[这里](http://embed.plnkr.co/41guX7lPUAccn2sWeObO/preview)查看ho~

