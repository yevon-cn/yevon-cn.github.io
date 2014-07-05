---
layout: post
date: 2014-05-16 21:06:00
title: "国内NPM镜像"
tags:
- NPM
- PhoneGap
- 镜像
- taobao
---

最近准备玩玩PhoneGap，在用npm安装PhoneGap的时候卡住了，这个跟rubygems一样，因为国外的东东，不稳定啊……

于是乎，之前玩jekyll的时候受过教训了，所以搜了一吧，搜到了国内的NPM镜像~

### 可选地址

[http://registry.npm.taobao.org][ref6] 淘宝的~
[http://registry.cnpmjs.org][ref5] 这个我真不了解~


### 使用方法

##### 方法一：通过config命令指定

{% highlight bash linenos %}
npm config set registry http://registry.cnpmjs.org 
npm info underscore （如果上面配置正确这个命令会有字符串response）
{% endhighlight %}


##### 方法二：在命令行中指定

{% highlight bash linenos %}
npm --registry http://registry.cnpmjs.org info underscore 
{% endhighlight %}


##### 方法三：在配置文件中指定，编辑 ~/.npmrc 加入下面内容

{% highlight bash linenos %}
registry = http://registry.cnpmjs.org
{% endhighlight %}


##### 方法四：利用定制的cnpm代替npm（具体参见[这里][ref3]）

{% highlight bash linenos %}
 #安装该模块，然后通过该命令来安装所需模块
 npm install -g cnpm --registry=http://registry.npm.taobao.org
 # 利用cnpm来安装模块
 cnpm install [name]
 # 利用cnpm来同步模块
 cnpm sync connect
{% endhighlight %}


##### 方法五：利用alias添加一个基于npm的新命令（基于方法二的封装，具体参见[这里][ref3]）

{% highlight bash linenos %}
 alias cnpm="npm --registry=http://registry.npm.taobao.org \
--cache=$HOME/.npm/.cache/cnpm \
--disturl=http://npm.taobao.org/dist \
--userconfig=$HOME/.cnpmrc"

#Or alias it in .bashrc or .zshrc
$ echo '\n#alias for cnpm\nalias cnpm="npm --registry=registry.npm.taobao.org \
  --cache=$HOME/.npm/.cache/cnpm \
  --disturl=http://npm.taobao.org/dist \
  --userconfig=$HOME/.cnpmrc"' >> ~/.zshrc && source ~/.zshrc
{% endhighlight %}


### 题外话

似乎搭建NPM镜像的方法就在[这里][ref4]。

### 参考资料

* [淘宝网提供的国内NPM镜像简介和使用方法][ref1]
* [使用npm安装一些包失败了的看过来（npm国内镜像介绍）][ref2]


[ref1]: http://www.jb51.net/article/49118.htm "淘宝网提供的国内NPM镜像简介和使用方法"
[ref2]: http://cnodejs.org/topic/4f9904f9407edba21468f31e "使用npm安装一些包失败了的看过来（npm国内镜像介绍）"
[ref3]: http://npm.taobao.org/ "淘宝 NPM 镜像"
[ref4]: https://github.com/cnpm/cnpmjs.org
[ref5]: http://cnpmjs.org
[ref6]: http://npm.taobao.org
