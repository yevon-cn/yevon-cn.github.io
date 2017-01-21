---
layout: post
date: 2012-03-09 10:15
title: javascript获取节点计算后的CSS样式
tags:
- 转录
- JavaScript
- CSS
- style
---

> 转录自：[javascript的getComputedStyle方法获取节点的计算后的CSS样式](http://www.cnblogs.com/yunfour/archive/2012/02/25/2367895.html)

今天在做东西的时候，遇到一个问题：想获取节点style指定的CSS属性，如：ele.style.display属性，如果在节点中没有设置其style.display属性的话则通过ele.style.display这种方式获取的值为空字符串。

<!--more-->

如果节点ele是一个块状元素的话，通过上述方式返回的display的值则应该为：block的，而其得到的值为空字符串并非我想得到的，在网上 查找了了一下，浏览器中提供了一个方法：window.getComputedStyle()的方法可以得到节点的计算后样式，该方法有两个参数，第一个 是要所要获取的样式的节点，第二个参数不知道是什么作用，网上给出的例子都将设置成null；即是这样调用 的：window.getComputedStyle(node,null)，其返回值为一个对象，为计算后的样式的属性值对的集合。

但是IE浏览器中不支持该方法，不过在IE中，元素节点有一个属性对应的也是该节点计算后的方法，例如，在IE中节点node计算后的样式 为：node.currentStyle，该属性是一个对象，也是计算后的样式的属性值对的集合。为了兼容性我们可以将其封装改写一下，提供一个统一的方 法getCurrentStyle(node)，如下：

```javascript
// 参数node：将要获取其计算样式的元素节点
function getCurrentStyle(node) {
    var style = null;
    
    if(window.getComputedStyle) {
        style = window.getComputedStyle(node, null);
    }else{
        style = node.currentStyle;
    }
    
    return style;
}
```
 

以下代码是获取其中div的display的样式属性值：

```html
<div id="div">div节点</div>
<script>
var div = document.getElementById("div");
var style = getCurrentStyle(div);
var display = style["display"];

alert(display);    // 输出：block
</script>
```