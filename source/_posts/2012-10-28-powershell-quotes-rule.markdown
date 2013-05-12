---
layout: post
title: "Powershell里的单引号君和双引号君"
date: 2012-10-26 22:38
comments: true
categories: [powershell]
---

Powershell里怎么使用单引号君`'`和双引号君`"`及单引号君的弟弟反单引号君`` ` ``呢？
<!-- more -->

### 规则1. 单引号（所见即所得） ###
{% codeblock lang:bash %}
Write-Host 'haha'
# haha

$a = 2
Write-Host '1 + 1 = $a'
# 1 + 1 = $a
{% endcodeblock %}

### 规则2. 双引号（代换内部变量） ###
{% codeblock lang:bash %}
Write-Host "haha"
# haha

$a = 2
Write-Host "1 + 1 = $a"
# 1 + 1 = 2
{% endcodeblock %}

### 规则3. 反单引号（取消变量代换，转义字符，换行连接符） ###
{% codeblock lang:bash %}
#取消变量代换
$a = 2
Write-Host "1 + 1 = `$a"
# 1 + 1 = $a

#转义字符
Write-Host "I said: `"Hello Bug~`""
# I said: "Hello Bug~"

#换行连接符
$a = 1 + `
2
Write-Host $a
# 3
{% endcodeblock %}

### 规则4. 想输出，写两遍 ###
{% codeblock lang:bash %}
#双引号里的单引号，单引号里的双引号，都直接写就可以输出了
Write-Host "ha'ha"
# ha'ha
Write-Host 'ha"ha'
# ha"ha

#双引号里的双引号，单引号里的单引号，写两遍就可以输出了
Write-Host "ha""ha"
# ha"ha
Write-Host 'ha''ha'
# ha'ha
{% endcodeblock %}