---
layout: post
title: "[AngularJS系列(2)] Scope什么的~"
date: 2013-03-15 20:47
comments: true
categories: [javascript, angularjs]
---

Scope是AngularJS里的一个很重要的概念，简单的说它就是用来保存AngularJS Model们的对象，是Model们温暖的小家～

那这个小家是什么时候造的呢？

<!-- more -->

{% codeblock lang:html %}
<html ng-app="mainApp">
</html>
{% endcodeblock %}

我们知道，`ng-app`是一个应用启动AngularJS的入口点，在这里也会创建一个root scope，在controller里可以通过`$rootScope`调到，每个应用只能有一个root scope（当然了～root嘛～），但它会有多个child scope，那啥时候会创建child scope呢？

{% codeblock lang:html %}
<html ng-app="mainApp">
    <body ng-controller="MainCtrl">
        <div ng-controller="SubCtrl" ng-include src=" 'template.html' ">
        </div>
        <ul>
            <li ng-repeat="item in items">{% raw %}{{item.name}}{% endraw %}</li>
        </ul>
    </body>
</html>
{% endcodeblock %}

在上面的例子里，`ng-controller` `ng-include` `ng-repeat`都创建了新的child scope（`ng-repeat`是对每一个重复的元素都创建了新的child scope），他们之间的父子关系是这样的：

{% img /my-images/angularjs-scope-1-1.png %}

包含关系即是他们的父子关系，子scope是可以访问父scope上绑定的所有model和function的。

AngularJS会给scope对应的dom添加叫ng-scope的class，如果我们给自己的应用加这样一个css~
{% codeblock lang:css%}
.ng-scope {border: 2px dotted red;}
{% endcodeblock %}
通过红色的虚线边框我们也可以看出来大概scope的范围，但注意，并不是所有的ng-scope都是新的scope，有些ng-scope类名对应的dom共享的是同一个scope。   

细心的童鞋可能注意到，`ng-controller="SubCtrl"`和`ng-include`放在同一个div上了，为啥`ng-controller="SubCtrl"`就是爸爸，`ng-include`就是儿子呢？   
这个没啥特别的原因，`ng-controller`在AngularJS底层代码里实现的比较靠前而已，与在div上标明的顺序无关，但是这时会发生一个问题：

假如在`ng-include`对应的`template.html`里有这样的代码：
{% codeblock lang:html template.html %}
<input type="text" ng-model="lastName" >
{% endcodeblock %}

我们会发现，在`ng-controller="SubCtrl"`这个controller里是取不到`lastName`的值的。

原因是这样的~   
我们假设`ng-controller="SubCtrl"`对应的是**Scope A**，`ng-include`对应的是**Scope B**~

1. `ng-include`创建的**Scope B**是`ng-controller`创建的**Scope A**的子scope，所以在`template.html`里可以访问**Scope A**的model和function。
2. 在`template.html`里用`ng-model`绑定的model，是存放在**Scope B**上的，**Scope A**是拿不到的，即使model同名。

解决方案：

- 直接对**Scope A**的model绑定成员对象，如`ng-model="user.lastName"`
- 或在`template.html`使用`ng-model`绑定model时，加上`$parent`（取父scope），如：`ng-model="$parent.lastName"`，这样info就绑定在**Scope A**上了

比较推荐第一种方式，因为第一种抽象出了对象，比起第二种所有的model都直接绑在$scope上来，封装的更好~

[这里是官方Scope介绍~](http://docs.angularjs.org/guide/scope)
