---
layout: post
title: "[AngularJS系列(5-2)] Directive - Compile vs. Link"
date: 2013-08-14 20:35
comments: true
categories: [javascript, angularjs]
---

还是先从一个栗子看起~

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

- html ng-blur后面可以跟的内容
- ng-show的用法
- link的另一种写法
- compile和link，从angular编译机制讲起
- 再举个compile的例子

456