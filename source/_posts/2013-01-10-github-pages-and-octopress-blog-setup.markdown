---
layout: post
title: "[Github Pages + Octopress 搭建篇] - Geek般的写博客呦~"
date: 2012-08-24 01:31
comments: true
categories: [blog, octopress, github]
---

一个偶然发现了[Octopress](http://octopress.org/)，研究了一下发现真的很有意思~ 其实原理呢就是[Github pages](http://pages.github.com/)提供了存储静态页面的空间域名和[Jekyll](https://github.com/mojombo/jekyll)（Blog生成工具），支持Markdown格式，而Octopress呢又把Jekyll包装了一下，使用起来更方便，更可以专注在内容上啦~ 好了，开始吧~
<!-- more -->

## 1. 准备Github Pages ##

首先，得有个github账户（假设你用户名是xiaoming），在里面创建一个Repository，名字一定要是`xiaoming.github.com`，你才能用`xiaoming.github.com`的域名去访问你的blog~

然后，在你Repository的`Admin`里，左边选`Option`，右边会看到`Github Pages`的项，点击`Automatic Page Generator`按钮到下一页后，直接点击最下面的`Continue to Layouts`，到达选择模板页，不用选择，一会儿会被Octopress的模板替换掉，直接点击`PUBLIS`按钮就发布成功了，等一段时间就能生效啦~

## 2. 准备本地环境 ##

官方的搭建说明文档在[这里](http://octopress.org/docs/setup/)。

由于工作和游戏的种种原因，我使用的“2B青年必备”的Mac下的Win7。。。-_-

#### a) Git ####
安装[Git](http://git-scm.com/downloads)

#### b) Ruby ####
安装[RubyInstaller](http://rubyforge.org/frs/?group_id=167) （>=1.9.2）

#### c) Gem ####
安装[DevKit](http://rubyinstaller.org/downloads/)  
然后在命令行执行：
{% codeblock lang:bash %}
cd path-to-devkit
ruby dk.rb init
ruby dk.rb instal
{% endcodeblock %}

_or_

#### 前三步可替换为RailsInstaller ###

安装[RailsInstaller](http://railsinstaller.org/) (包括了Ruby，Git，Gem)

#### d) python ####

安装[Python](http://www.python.org/getit/)，因为代码高亮系统是基于Python编写的。

## 3. 更改本地配置 ##

#### a) 中文utf-8编码的支持 ####

在环境变量里设置`LANG=zh_CN.UTF-8`，`LC_ALL=zh_CN.UTF-8`

#### b) 把gem的更新源改为taobao，因为官方的更新源总是被河蟹-_- ####
{% codeblock lang:bash %}
gem sources --remove http://rubygems.org/
gem sources -a http://ruby.taobao.org/
gem sources -l #这里的输出要确保只有taobao一个源
{% endcodeblock %}

#### c) 安装rdoc和bundler ####
{% codeblock lang:bash %}
gem install rdoc bundler
{% endcodeblock %}

## 4. 安装Octopress ##
找个合适的目录，执行下面的命令
{% codeblock lang:bash %}
# clone octopress代码
git clone git://github.com/imathis/octopress.git octopress
cd octopress

# 修改octopress的gem源
vi Gemfile # 或notepad Gemfile
将行: source "http://rubygems.org/"
改为: source "http://ruby.taobao.org/"

# 安装octopress所需的gem组件
bundle install

# 安装默认主题
rake install
{% endcodeblock %}

## 5. 关联Github Pages ##

#### a) 与Github建立连接 ####
{% codeblock lang:bash %}
rake setup_github_pages
# 按照提示输入Github Pages Repository的url地址，如：git@github.com:xiaoming/xiaoming.github.com.git
# 这时创建了一个githug分支用来保存source文件
{% endcodeblock %}

#### b) 更新blog配置文件 ####
更新配置文件`octopress/_config.yml`，可参考[官网说明](http://octopress.org/docs/configuring/)。
这里就可以给blog起标题副标题神马的了~

#### c) 生成静态页面 ####
{% codeblock lang:bash %}
rake generate
{% endcodeblock %}

#### d) 本地预览 ####
{% codeblock lang:bash %}
rake preview
# 可以访问http://localhost:4000查看博客本地效果
{% endcodeblock %}

#### e) 发送到github上 ####
{% codeblock lang:bash %}
rake deploy
# 可以访问http://xiaoming.github.com查看实际效果
{% endcodeblock %}

#### f) 保存source文件到github source分支上 ####
{% codeblock lang:bash %}
git add .
git commit -m "xiaoming blog source"
# 这里如果要pull，一定要从分支pull
# git pull origin source
git push origin source
{% endcodeblock %}

## 6. 开始写博客啦。。。终于。。。 ##
{% codeblock lang:bash %}
# 创建新页面
rake new_page["page name"]
# 创建新博文
rake new_post["blog title"]
{% endcodeblock %}

官方说明在[这里](http://octopress.org/docs/blogging/)

#### 然后。。。就没有然后了。。。才怪。。。改天继续总结。。。 ####
#### ^_^ ####