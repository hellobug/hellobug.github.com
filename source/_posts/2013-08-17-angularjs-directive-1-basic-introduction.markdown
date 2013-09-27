---
layout: post
title: "[AngularJS系列(5-1)] Directive - 开场小介绍"
date: 2013-08-04 22:35
comments: true
categories: [javascript, angularjs]
---

Directive其实就是让html变得更强大的一种方法。它可以根据需求对dom变形，或注入行为。

觉得它很神秘么，其实一点儿也不神秘，只要开始使用AngularJS了，就一定在使用着Directive，比如我们在html上写的那些`ng-controller`，`ng-model`，`ng-show`等等都是AngularJS提供的Directive啊~

那到底内部是怎么实现的呢？或者如果觉得AngularJS内置提供的Directive不给力咋办？现在咱们就自己做一个试试看吧~

<!-- more -->

{% img /my-images/angularjs-directive-1-address-inputs.png %}

先来个比较简单的需求，假如我们的应用经常要用到地址信息的输入选项，如果不自定义directive，html要写成这样~

{% codeblock lang:html %}
<div class="address">
	<label>
		国家:<input type="text" ng-model="country" />
	</label>
	<label>
		城市:<input type="text" ng-model="city" />
	</label>
	<label>
		街道详情:<input type="text" ng-model="address" />
	</label>
	<label>
		邮编:<input type="text" ng-model="zipcode" />
	</label>
</div>
{% endcodeblock %}

这样的代码可能要写多次，给维护也添加了复杂度。
如果自定义个地址的directive，代码如下：

{% codeblock lang:html 之前的html %}
<div my-address> </div>
{% endcodeblock %}

{% codeblock lang:html 抽出来的html模板，名称为address.html %}
<div class="address">
	<label>
		国家:<input type="text" ng-model="country" />
	</label>
	<label>
		城市:<input type="text" ng-model="city" />
	</label>
	<label>
		街道详情:<input type="text" ng-model="address" />
	</label>
	<label>
		邮编:<input type="text" ng-model="zipcode" />
	</label>
</div>
{% endcodeblock %}

{% codeblock lang:js directive的定义 %}
app.directive("myAddress", function() {
  return {
    restrict: "A",
    templateUrl: "address.html",
    replace: true
  }
})
{% endcodeblock %}

## 命名
可能有人注意到了，在html里标签上定义的属性叫`my-address`，可是在js里定义directive时写的名字是`myAddress`，这能对应上么？

其实是可以的，不仅如此，下列格式都可以识别出来~

- my:address
- my-address (推荐)
- my_address
- x-my-address
- data-my-address

但是由于html不区分大小写和标准写法是用中横线`-`的特性，比较推荐`my-address`这种写法，既简洁又遵循html的规则。

## 声明形式限制
`restrict: "A"` 这一句表示对directive声明形式的限制，一共有四种：

- `E`: `<my-address></my-address>` Element Name 标签名
- `A`: `<div my-address="exp"></div>` Attribute 属性名（默认值）
- `C`: `<div class="my-address: exp;"></div>` Class 类名
- `M`: `<!-- directive: my-address exp -->` Comment 注释

其实不写也可以，因为如果不写，默认就是`restrict: "A"`

如果想支持多项，可以写成`restrict: "EA"`，这样就又支持标签的写法，又支持属性的写法

虽然一共有四种可以选择，但后两种并不推荐~

- 类应该更多的是与样式相关，而且将directive名混杂在一群类名里，可读性也不好
- 注释就更不可靠了，将重要的逻辑写在注释里，太容易被忽略了

## Html内容模板指定
`templateUrl: "address.html"`指定了模板的Url路径，如果想直接写html内容也可以用：  
`template: "<div class='address'>blabla</div>"`

下面的`replace: true`表示模板html内容是否会替换`<div my-address></div>`，还是插入到`<div my-address></div>`元素里，默认是`false`

## 最后
这样一个简单的directive就写好了，其实如果只是想抽html模板的话，还可以用`ng-include`，相当上上面例子里`replace:false`的效果~

{% codeblock lang:html 使用ng-include的版本 %}
//注意这里是单引号套着双引号，外面一层引号是表示标签属性的值，里面一层引号表示直接给的模板名称，因为这里也可以通过表达式的返回值来取
<div ng-include='"address.html"'> </div>
{% endcodeblock %}

貌似更简单。。。那前面说那么多干嘛？！  
这不是为了熟悉directive嘛~ -_-|||

想自己动手试试，就猛戳[这里](http://plnkr.co/edit/ZgRpLtlRPsGWL14m7J3p)吧~

其实directive的世界搭滴恨(小纪念一下在西安悠哉的小日子)，下集待续。。。





 





