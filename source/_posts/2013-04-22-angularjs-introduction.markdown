---
layout: post
title: "[AngularJS系列(1)] 感受篇~"
date: 2013-03-12 20:10
comments: true
categories: [javascript, angularjs]
---

先来俩栗子感受一下~

<!-- more -->

`栗1`现在有个需求，希望页面上某段文字及时的显示某个输入框的输入内容~

如果我们用jQuery来写是这样的：
{% codeblock lang:html 栗1 - jQuery版本 %}
<h1>Hello, <span id="namePlaceholder"></span></h1>
<input id="name" type="text">

<script type="text/javascript">
	$('#name').keydown(function(e){
		$('#namePlaceholder').html(e.target.value);
	});
</script>
{% endcodeblock %}

如果用AngularJS来写呢，是这样的：
{% codeblock lang:html 栗1 - AngularJS版本 %}
<h1>Hello, {% raw %}{{name}}{% endraw %}</h1>
<input type="text" ng-model="name">
{% endcodeblock %}

`栗2`又来个需求，有个人名的数组，想把这些名字列到页面上~

{% codeblock lang:html 栗2 - jQuery版本 %}
<ul id="list"></ul>

<script type="text/javascript">
	['张三', '李四', '王五'].forEach(function(item){
		$('#list').append($('<li>' + item + '</li>'));
	});
</script>
{% endcodeblock %}

{% codeblock lang:html 栗2 - AngularJS版本 %}
<ul>
	<li ng-repeat="item in ['张三', '李四', '王五']">{% raw %}{{item}}{% endraw %}</li>
</ul>
{% endcodeblock %}

通过比较，可以发现[AngularJS](http://angularjs.org/)：

- ** 代码更简洁 **（少写好多js代码啊~）
- ** 扩展了html的功能 **（html貌似变的很强大哇！）
- ** 实现了model和view的双向绑定 **（随时在input里输入，随时通过`ng-model`绑定到`name`这个model上~）

其实[AngularJS](http://angularjs.org/)还：

- ** 分离了JS逻辑和页面渲染 **（本来JS又要忙数据交互，又要忙业务逻辑，又要忙页面渲染，现在html长大了，可以分担了~）
- ** 为JS提供了MVC框架 **（我们熟悉的MVC~）
- ** 提供了One Page Application流畅的用户体验 **（不像以前，每换一个页面都要重新向后台申请，现在是华丽的页面间无缝切换啊~）
- ** 提供依赖注入机制 **（意味着更支持模块化~）
- ** 便于写单元测试 **（行为和界面分离后，行为很方便测试啊~质量更有保障~）

以后会慢慢介绍这些优点，先来看一个完整的AngularJS应用：

{% codeblock lang:html main.html %}
<!doctype html>
<html ng-app="myApp">
	<head>
  		<script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/angularjs/1.0.6/angular.min.js"></script>
  		<script type="text/javascript" src="app.js"></script>
  	</head>
  	<body>
  		<h1>Hello, {% raw %}{{name}}{% endraw %}</h1>
  		<input type="text" ng-model="name">
  		<ul>
  			<li ng-repeat="item in nameList">{% raw %}{{item}}{% endraw %}</li>
  		</ul>
  	</body>
</html>
{% endcodeblock %}

{% codeblock lang:js app.js %}
var app = angular.module('myApp', []);   

app.controller('MainCtrl', function($scope) {
  $scope.nameList = ['张三', '李四', '王五'];
});
{% endcodeblock %}

** `ng-app` **:    
AngularJS的入口点。从这里开始，AngularJS系统就开始工作了~后面对应的`myApp`是在`app.js`文件里定义的angular module，module上注册了一个controller `MainCtrl`，model都是绑定在它对应的`$scope`上的。Scope的概念以后会细讲。

** `ng-model` **:   
在所有表单输入元素(input, select, textarea)上都可以用`ng-model`将value和model绑定起来，所输入的数值会直接被赋到对应的model上。

** `ng-repeat` **:   
遍历数组`nameList`重复当前页面元素。   
      
** 其它扩展过的标签属性们 **：  
[官方API](http://docs.angularjs.org/api)  




