---
layout: post
title: "[AngularJS系列(4)] 那伤不起的provider们啊~ (provider, value, constant, service, factory, decorator)"
date: 2013-04-23 22:35
comments: true
categories: [javascript, angularjs]
---

用AngularJS做项目，但凡用过什么service啊，factory啊，provider啊，开始的时候晕没晕？！晕没晕？！感觉干的事儿都差不多啊，到底用哪个啊？！别告诉我你们几个就是为了跟我炫耀兄弟多！！{% img /my-images/emotions/xianzhuozi.jpg %}  

好吧。。。也许是我的问题，脑仁儿确实不够大，反正我是晕的直挠墙~

那到底什么时候该请他们谁出场啊？

经过挠墙之后挠官网文档挠google挠源码挠例子试验，终于让我把他们的区别给挠出来了！（得意的笑～～）

首先，`provider`, `value`, `constant`, `service`, `factory`他们都是provider！（`decorator`小朋友先搬个小板凳坐在边上等会儿，现在还没轮到你出场哈~）

provider是干啥的？

<!-- more -->

provider可以为应用提供通用的服务，形式可以是常量，也可以是对象。

比如我们在controller里常用的`$http`就是AngularJS框架提供的provider～

{% codeblock lang:js %}
myApp.controller(‘MainController', function($scope, $http) {
    $http.get(…)
}
{% endcodeblock %}

在上面的代码里，就可以直接使用`$http`包好的各种功能了～   


# provider

那我们自己想定制一个provider，怎么写呢～

{% codeblock lang:js %}
//定义:
$provide.provider('age', {
    start: 10,
    $get: function() {
      return this.start + 2;
    }
});
//或
$provide.provider('age', function($filterProvider){
    this.start = 10;
    this.$get = function() {
      return this.start + 2;
    };
});

//调用:
app.controller('MainCtrl', function($scope, age) {
  $scope.age = age; //12
});

{% endcodeblock %}

provider的基本原则就是通过实现`$get`方法来在应用中注入单例，使用的时候拿到的`age`就是`$get`执行后的结果。
上面例子中的两种定义方法都可以～

# factory

大哥`provider`每次出场太繁琐了，如果我就想定义个`$get`，没别的乱七八糟的呢？这时候该二哥`factory`出场了～

（脑补背景音乐：葫芦娃～葫芦娃～一根藤上七朵花～[我说你够了啊！>_<]）

{% codeblock lang:js %}
$provide.provider('myDate', {
    $get: function() {
      return new Date();
    }
});
//可以写成
$provide.factory('myDate', function(){
    return new Date();
});

//调用:
app.controller('MainCtrl', function($scope, myDate) {
  $scope.myDate = myDate; //current date
});
{% endcodeblock %}

直接第二个参数就是$get要对应的函数实现，代码简单了很多有没有？！

# service

这时候我又来劲儿了，我不仅就想定义个`$get`，里面我还就返回个new出来的已有js类，三哥`service`闪亮登场～

{% codeblock lang:js %}
$provide.provider('myDate', {
    $get: function() {
      return new Date();
    }
});
//可以写成
$provide.factory('myDate', function(){
    return new Date();
});
//可以写成
$provide.service('myDate', Date);
{% endcodeblock %}

对于这种需求，代码更简洁了是不是～～

# value vs. constant

更直接的需求来了，我只想定义个`$get`，而且就返回个常量～

这时候`value`和`constant`都可以做到～

{% codeblock lang:js %}
$provide.value('pageCount', 7);
$provide.constant('pageCount', 7);
{% endcodeblock %}

兄弟俩功能一样？双胞胎？那怎么可能～

介绍区别前，先得把之前坐小板凳等着的`decorator`叫出来～终于该出场了～

### 区别一：`value`可以被修改，`constant`一旦声明无法被修改

{% codeblock lang:js %}
$provide.decorator('pageCount', function($delegate) {
    return $delegate + 1; 
});
{% endcodeblock %}

`decorator`可以用来修改（修饰）已定义的provider们，除了`constant`～

### 区别二：`value`不可在`config`里注入，`constant`可以

{% codeblock lang:js %}
myApp.config(function(pageCount){
    //可以得到constant定义的'pageCount'
});
{% endcodeblock %}

关于`config`，之后会专门介绍～

## 通过底层实现代码看关系～

{% codeblock lang:js %}
function provider(name, provider_) {
    if (isFunction(provider_)) {
        provider_ = providerInjector.instantiate(provider_);
    }
    if (!provider_.$get) {
        throw Error('Provider ' + name + ' must define $get factory method.');
    }
    return providerCache[name + providerSuffix] = provider_;
}

function factory(name, factoryFn) { return provider(name, { $get: factoryFn }); }

function service(name, constructor) {
    return factory(name, ['$injector', function($injector) {
        return $injector.instantiate(constructor);
    }]);
}

function value(name, value) { return factory(name, valueFn(value)); }

function constant(name, value) {
    providerCache[name] = value;
    instanceCache[name] = value;
}

function decorator(serviceName, decorFn) {
    var origProvider = providerInjector.get(serviceName + providerSuffix),
        orig$get = origProvider.$get;

    origProvider.$get = function() {
        var origInstance = instanceInjector.invoke(orig$get, origProvider);
        return instanceInjector.invoke(decorFn, null, {$delegate: origInstance});
    };
}
{% endcodeblock %}

从上面的代码可以看出之前介绍的每种provider的特点，`decorator`比较特殊，不算，除了`constant`，另外几个最终调用的都是`provider`～

## 最后再总结一下provider哥儿几个的优点～

#### 1. 为应用提供通用的服务，形式可以是常量或对象
#### 2. 便于模块化
#### 3. 便于单元测试






