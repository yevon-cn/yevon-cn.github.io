---
layout: post
date: 2013-12-28 20:42:00
title: document.defaultView
tags:
- 转录
- JavaScript
---

> 摘录自：[document.defaultView](https://developer.mozilla.org/zh-CN/docs/DOM/document.defaultView)

### 概述

在浏览器中，该属性返回当前`document`对象所关联的`window`对象，如果没有，会返回`null`。

<!--more-->

### 语法

```javascript
var win = document.defaultView;
```

*该属性只读.*

### 备注

根据`quirksmode`，`IE 9`以下版本不支持`defaultView`。

### 规范

* [HTML5: defaultView](https://developer.mozilla.org/zh-cn/HTML/HTML5)
* [DOM Level 2 Views: defaultView](http://www.w3.org/TR/DOM-Level-2-Views/views.html#Views-DocumentView-defaultView)
* [DOM Level 3 Views](http://www.w3.org/TR/DOM-Level-3-Views/) (Only developed to Working Group Note and not implemented)