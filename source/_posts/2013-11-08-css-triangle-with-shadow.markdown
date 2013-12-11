---
layout: post
title: "[CSS] 用CSS画三角形，普通版，文艺小阴影版~"
date: 2013-09-28 19:04
comments: true
categories: [css]
published: false
---

# 普通版

用CSS画个普通三角形，应该已经不是时髦的技术了，现在用的比较普及了，不过还是先讲一下原理吧~

<!-- more -->

先来看一下盒子模型里内容和border的关系~为了更容易看清楚，我把四面的border分别设置成不同的颜色~

{% codeblock lang:html HTML %}
<div></div>
{% endcodeblock %}

{% codeblock lang:css Style %}
div {
    width: 80px;
    height: 80px;
    border: 20px solid red;
    border-left-color: blue;
    border-bottom-color: orange;
    border-right-color: green;
}
{% endcodeblock %}

效果如下：    

{% img /my-images/css-triangle-01.png %}

那如果我们继续缩小内容的宽高，直到0呢~

{% codeblock lang:css Style %}
div {
    width: 0;
    height: 0;
    border: 20px solid red;
    border-left-color: blue;
    border-bottom-color: orange;
    border-right-color: green;
}
{% endcodeblock %}

效果如下：    

{% img /my-images/css-triangle-02.png %}

这个时候就很清晰了，我们只要根据需求隐藏相应的border，就剩下一个三角形了~比如：

{% codeblock lang:css Style %}
div {
    width: 0;
    height: 0;
    border: 20px solid red;
    border-left-color: transparent;
    border-bottom-color: transparent;
    border-right-color: transparent;
}
{% endcodeblock %}

铛铛档~~新鲜的向下的小红三角形就闪亮出场了~~~   

{% img /my-images/css-triangle-03.png %}

留着上border和左border就是向左上的三角形~  

{% img /my-images/css-triangle-04.png %}

通过调整不同方向border的宽度还可以得到不同比例的三角形，比如~   

{% img /my-images/css-triangle-05.png %}

#### 原理明白了后可能有两个问题就冒出来了：
   
- 什么场景会用到三角形？
- 为什么不用图片，而要用样式来写？

#### 先来说场景~

来一些网站上的截图：

{% img /my-images/css-triangle-scene-01.png %}
{% img /my-images/css-triangle-scene-02.png %}
{% img /my-images/css-triangle-scene-03.png %}

如果留意的话，就会发现三角形在网站中是个很常用的显示元素呢~

#### 再来说为啥不推荐用图片~

是的，这些三角形用图片都是可以达到一样的效果的，但当颜色和比例需要改动时，CSS只要简单的改变一下border的样式就可以了，图片可是要重新替换的，效果也没有那么容易调试，过多的图片也会对页面性能造成影响，所以还是推荐用CSS来实现喽~

# 文艺小阴影版

慢慢的，我们希望做出更炫的界面，比如希望有阴影效果~









