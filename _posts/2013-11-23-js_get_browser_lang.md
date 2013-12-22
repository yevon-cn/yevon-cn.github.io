---
layout: post
title: "JavaScript获取浏览器语言类型"
date: 2013-11-23 17:30:00
tags: 
- JavaScript
- Language
- Browser
---
因为一些功能需要，要在js中获取用户浏览器的语言配置，比如Disqus，根据用户浏览器的语言来显示加载指定语言的界面，而不是根据配置的唯一一种语言。

> 下文转录自：[JavaScript获取浏览器语言类型](http://kevinpeng.iteye.com/blog/1332662)


获取浏览器语言： 
IE：navigator.browserlanguage 
返回："zh-cn" 

FF及其他浏览器：navigator.language 
返回："zh-CN" 
