---
layout: post
date: 2013-12-28 18:30:00
title: 关于getComputedStyle
tags:
- JavaScript
- CSS
- style
- getComputedStyle
- currentStyle
---

很多时候，在用js操作页面元素时，需要获取元素的CSS样式值，但是可能我们事先并没有给它设定样式，这种情况下就有点尴尬了。

不过既然有这方面需求，那么浏览器就会有提供这类方法，给予开发者调用。

### window.getComputedStyle

就如方法名称所表达的意思一样，即获得浏览器最终渲染计算后所得的最终样式值，当然不同浏览器所体现的效果可能有细微差别。

##### 语法

{% highlight javascript linenos%}
var style = window.getComputedStyle(element[, pseudoElt]);
{% endhighlight %}

###### element

要获取样式的元素。

###### pseudoEle

用于匹配伪元素的字符串。某些浏览器下是*可选*的，必要时传递`null`即可。

###### 返回值

返回一个[CSSStyleDeclaration][]对象。


###### 使用实例 *引自[getComputedStyle][]*

{% highlight html linenos%}
<style>
 h3:after {
   content: ' rocks!';
 }
</style>

<h3>generated content</h3> 

<script>
  var h3       = document.querySelector('h3'), 
      result   = getComputedStyle(h3, ':after').content;

  console.log('the generated content is: ', result); // returns ' rocks!'
</script>
{% endhighlight %}



**PS：**CSS属性值涉及到三种类型的值，按顺序分别是：指定值([Specified value][])、计算值([Computed value][]、应用值([Used value][])。而这里的getComputedStyle获取的是最终的应用值。另外根据[某文档][cascade]显示，似乎还有第四种值，实际值(Actual value)，不过一般情况下这个跟应用值是一样的。

**PPS：**Firefox还支持一个`window.getDefaultComputedStyle`方法，用于获取浏览器忽略指定值的计算值（即浏览器默认样式值），参数同`window.getComputedStyle`。

#### element.currentStyle

虽然有window.getComputedStyle方法，但是在IE下面还是力不从心…… 没错，[IE9][]以下并不支持，不过早先IE有在元素上实现一个currentStyle方法，可获得元素当前的样式，但这并不是[Used value][]，据我推测顶多到[Specified value][]，即如果没有指定css样式([Specified value][])，则获得的是浏览器默认样式值。区别如下(差别还是挺大的)：

{% highlight html linenos%}
<style>
 body {
   height: 100%;
 }
 #body {
 	position: absolute;
 }
</style>
<body style="with:auto;">
	This is the <a id="body">body</a>.
</body>
<script>
	var body = document.getElementById('body');

  //通过style获取样式 in IE8、Chrome、Firefox
  document.body.style.width; //"auto"
  document.body.style.height; //"" 
  body.style.display; //""
  body.style.position; //""

  // in IE8
  document.body.currentStyle.height; //"100%"
  document.body.currentStyle.width; //"auto"
  body.currentStyle.display; //"inline" 
  body.currentStyle.position; //"absolute"

  // in Chrome or Firefox
  window.getComputedStyle(document.body).width; //"1298px"
  window.getComputedStyle(document.body).height; //"324px"
  window.getComputedStyle(body).display; // "block"
  window.getComputedStyle(body).position; // "absolute"

</script>
{% endhighlight %}

上面代码可以说明几点：

* 通过元素的`style`属性能获取到的是行内样式，因此通过该方式设置的样式也是位于行内样式上的。
* IE的`currentStyle`方法获取的应该是指定值。因为`id`为body的`a`标签的`position`属性已经`absolute`了，而获取到的`display`还是`inline`，说明未计算，计算值与指定值的区别应该就在此了。
* 上面的代码看上去怪怪的，说明我写得容易让人误解，所以看仔细点啊…… (-_-)

另外在Microsoft的[Internet Explorer Test Drive][]上有[关于currentStyle和getComputedStyle的测试对比][ietest]，当然前提是用IE9以上版本(我不确定IE9是否支持哈~)才能看到对比效果哦。


### 参考资料
* [getComputedStyle][]
* [Specified value][]
* [Computed value][]
* [应用值][Used value]
* [cascade][]
* [CSSStyleDeclaration][]
* [getComputedStyle method][IE9]
* [Test IE getComputedStyle and currentStyle][ietest]


[getComputedStyle]: https://developer.mozilla.org/en-US/docs/Web/API/window.getComputedStyle
[Specified value]: https://developer.mozilla.org/en-US/docs/CSS/specified_value
[Computed value]: https://developer.mozilla.org/en-US/docs/CSS/computed_value
[Used value]: https://developer.mozilla.org/zh-CN/docs/CSS/used_value
[cascade]: http://www.w3.org/TR/CSS2/cascade.html
[CSSStyleDeclaration]: https://developer.mozilla.org/en-US/docs/DOM/CSSStyleDeclaration
[IE9]: http://msdn.microsoft.com/library/windows/apps/hh702516.aspx
[ietest]: http://ie.microsoft.com/testdrive/HTML5/getComputedStyle/
[Internet Explorer Test Drive]: http://ie.microsoft.com/testdrive/Default.html