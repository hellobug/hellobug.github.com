---
layout: post
title: "[CSS] 用CSS画三角形，普通版，文艺小阴影版~"
date: 2013-11-18 19:04
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

慢慢的，我们希望做出更加高大上的界面，比如希望三角形有阴影效果~

如果我们把阴影样式加在刚才的三角形上：

{% codeblock lang:css Style %}
div {
    width: 0;
    height: 0;
    border: 40px solid red;
    border-left-color: transparent;
    border-bottom-color: transparent;
    border-right-color: transparent;
    -webkit-box-shadow: 0 0 5px rgba(0, 0, 0, 0.3);
    box-shadow: 0 0 5px rgba(0, 0, 0, 0.3);
}
{% endcodeblock %}

{% img /my-images/css-triangle-shadow-01.png %}

结果会发现，这根本不是我们想要的样式~    
`box-shadow`只会出现在border的位置，没办法让它沿着三角形的边来显示~

再次铛铛铛~几种替换绝招出场啦~~~

## Unicode替身法

Unicode符号里有上下左右方向的三角形 ▲ ▼ ◀ ▶，如果我们直接用这种符号，给上阴影效果就可以了

{% codeblock lang:html Html %}
<span class="triangle">▼</span>
<span class="unicode"></span>
{% endcodeblock %}

{% codeblock lang:css Style %}
.triangle {
    color: red;
    font-size: 40px;
    text-shadow: 0 0 7px rgba(0, 0, 0, 0.7);
}
/* 或者用content的方式 */
.unicode:after {
    content: '\25BC';
    color: blue;
    font-size: 40px;
    text-shadow: 0 0 7px rgba(0, 0, 0, 0.7);
}
{% endcodeblock %}

效果都是一样的~

{% img /my-images/css-triangle-shadow-02.png %}

这个方法虽然简单，但也有很多不足，比如三角形的比例不能调整，不同的浏览器对unicode符号渲染的效果经常会有差异，IE9及以下的版本不支持`text-shadow`，需要特别hack。

## 双盒旋转大法



## 老老实实上图法










