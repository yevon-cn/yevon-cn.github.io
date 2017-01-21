---
layout: post
title: "还没更换RubyGems镜像？"
date: 2013-10-30 21:33:32
tags: 
- Ruby
- RubyGems
- gem
- 镜像
- taobao
---

相信用过`Ruby`的人都知道`gem install`命令，但是在国内该命令安装的速度甚是不稳定（你懂的），导致尝试数次便是等待数时，记得之前在安装`redmine`的时候便是如此，之前不懂什么意思，还以为是命令不对呢……

<!--more-->

最近看到某网站上某介绍安装`jekyll`的[文章][freehao123]时，发现用到了一个奇怪的命令，里面还有[taobao][taobao_ruby]的字样，起初以为眼花了哈，后来尝试打开了命令里的链接，发现原来是更改`gem`命令的下载源，而该下载源的镜像便是在`taobao.org`域名上，甚是激动~ （感谢天、感谢地、感谢……）

很遗憾的是，目前我还没去体验过那个高速的感觉，但是这个绝对是真实可用的~

**用法大概如下：**
```bash
# 移除默认的源
$ gem sources --remove http://rubygems.org/
# 添加新的源
$ gem sources -a http://ruby.taobao.org/
# 查看更改是否生效
$ gem sources -l
*** CURRENT SOURCES ***

http://ruby.taobao.org
# 请确保只有 ruby.taobao.org
$ gem install rails
```

**后记**
似乎每次都要重新配置下啊~

**参考资料：**
>[免费开源Github Pages空间可绑域名搭建个人博客存放图片文件][freehao123]
>[RubyGems 镜像 - 淘宝网][taobao_ruby]


[freehao123]: http://www.freehao123.com/github-pages/2/ "免费开源Github Pages空间可绑域名搭建个人博客存放图片文件"
[taobao_ruby]: http://ruby.taobao.org/ "RubyGems 镜像 - 淘宝网"