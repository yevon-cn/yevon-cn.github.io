---
layout: post
title: "话说Form标签的target属性-----无刷新表单提交"
date: 2013-10-08 22:10:32
h_css: highlight_monokai
tags: 
- HTML
- form
- target
---
国庆前(2013)无聊，就在铁道部的[12306]上“逛”了下下。

**PS：**原来之所以叫12306，是因为其客服号码是12306，好吧，我很无知……

<p></p>

首先是被“逛”的页面：[票价查询]。

之所以去逛，是因为一直听闻该网站在节假日期间抢票慢、难，而国内浏览器厂商等开发抢票插件等等，这让我有些心动，于是便去瞧了瞧“传说”中的12306。

对于该页面的第一反应是，功能应该可以的，不然大家抢不到票了，不过美观的角度就不怎么样了。

本以为该页面会是用Ajax方式提及获取数据（因点查询后页面并无刷新迹象，且[余票查询]是Ajax请求方式的），于是通过Firebug观察提交的方式及返回的内容，发现是以POST方式的表单提交，而返回的内容居然是如下形式的代码：
{% highlight html linenos %}
<script>
parent.document.getElementById("randCode").value="";
parent.document.getElementById("img_rrand_code").src="passCodeActi0n.do?rand=rrand"+'?'+Math.random();

parent.document.getElementById("stationDIV").innerHTML="<strong>北京</strong>→<strong>上海</strong>列车票价信息暂无";
parent.document.getElementById("stationDIV2").innerHTML="<strong>上海</strong>→<strong>北京</strong>列车票价信息暂无";
{%raw%}<!-- =====================================================返程========================================================== -->{%endraw%}
parent.document.getElementById("stationDIV2").style.display='none';
parent.document.getElementById("gridbox2").style.display='none';
</script>
{% endhighlight %}

返回的这个信息让人非常不解，一般POST提交都是会“跳转”页面的，而这里返回了似乎是html代码的script标签，内部是含有返回数据的js代码。（代码和数据这两家伙，这么亲密-_-!）
 
 **对此我有两个疑问：**
1) 表单提交返回了这个代码，但是页面怎么没刷新呢？
2) 很明显返回的代码起了作用，因为查询结果出现在了页面上，咋回事？

细心的人可能注意到了上面的代码中一个明显的地方（我表示当时我没注意这个-.-），即parent.document字样，这个是在iframe嵌套时，调用父iframe中window的操作，明显这里有蹊跷，当前页面提交后，返回的代码中居然是通过parent.document字样的js代码来操作页面的。

另外，页面中表单标签的代码如下：
{% highlight html linenos %}
<form action="iframeTicketPriceByStation.jsp"
method="post" name="ticketPriceByStationform"
target="iframeName1">
  <!-- 此处略去x行代码 -->
</form>
{% endhighlight %}

这里的target属性让我感到可疑，特别是搜索target的值“iframeName1”后，在页面中有如下iframe代码，该iframe隐藏了：
{% highlight html linenos %}
<iframe name="iframeName1" id="iframeID1" src="" width="0" height="0"
frameborder="0" />
{% endhighlight %}

初步猜测表单提交后返回的页面加载到该iframe中了，对form的target做些调查后便能确定下来了。


 查询w3school上的介绍：target 属性规定在何处打开 action URL。

 不过对于该属性的使用上却有些“兼容性”的问题：
>在 HTML 4.01 中，不赞成使用 form 元素的 target 属性；在 XHTML 1.0 Strict DTD 中，不支持该属性。（[链接][html4_target]）
>在 HTML5 中 target 属性不再是被废弃的属性。（[链接][html5_target]）

通过查看[XHTML 1.0 Strict DTD][xhtml_dtd_strict]对form的定义，已经没有target属性了：
{% highlight dtd linenos %}
<!ATTLIST form
  %attrs;
  action      %URI;          #REQUIRED
  method      (get|post)     "get"
  enctype     %ContentType;  "application/x-www-form-urlencoded"
  onsubmit    %Script;       #IMPLIED
  onreset     %Script;       #IMPLIED
  accept      %ContentTypes; #IMPLIED
  accept-charset %Charsets;  #IMPLIED
  >
{% endhighlight %}

但在[XHTML 1.0 Transitional DTD][xhtml_dtd_transitional]中还保留着：
{% highlight dtd linenos %}
<!ATTLIST form
  %attrs;
  action      %URI;          #REQUIRED
  method      (get|post)     "get"
  name        NMTOKEN        #IMPLIED
  enctype     %ContentType;  "application/x-www-form-urlencoded"
  onsubmit    %Script;       #IMPLIED
  onreset     %Script;       #IMPLIED
  accept      %ContentTypes; #IMPLIED
  accept-charset %Charsets;  #IMPLIED
  target      %FrameTarget;  #IMPLIED
  >
{% endhighlight %}

看来该属性一度有被遗弃的现象，虽然在HTML5中已能正常使用了，但对于该属性的使用我还是不怎么习惯，总觉得有些另类哈……

以上介绍已经明了，该属性指定在何处打开表单提交的url，按照我们平时的使用可推测，默认情况是在当前页面。

参考对Target属性的介绍，它可以有如下值，这些值应该是很熟悉的了，涉及到链接的时候，都会有这几个出现：

|值|描述|
|---|---|
|_blank|在新窗口/选项卡中打开。|
|_self|在同一框架中打开。（默认）|
|_parent|在父框架中打开。|
|_top|在整个窗口中打开。|
|framename|在指定的框架中打开。|

很明显，票价查询页面上用的是framename这类型的值，起初在看该页面中的iframe标签属性是还让人有点迷糊，因为这里给iframe加了相同的id和name，这是一种习惯，但也让人疑惑target中放入的到底是id还是name？

以下是我的测试代码：
1）主页面：
{% highlight html linenos %}
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>The Target of Form</title>
  </head>
  
  <body>
        <form action="js.html" method="post" target="iframeName1">
            <input type="submit" value="test1" />
        </form>
        <iframe name="iframeName1"  id="iframeID1" src="" width="0" height="0" frameborder="0" >
        </iframe>
        
        <form action="js.html" method="post" target="iframeName2"> <!-- 指定name，如果不存在，这个name作为新开窗口的window.name  -->
            <input type="submit" value="test2" />
        </form>
        <iframe  id="iframeID2" src="" width="0" height="0" frameborder="0" >
        </iframe>
  </body>
</html>
{% endhighlight %}	

2）表单提交的页面（虽然可以只写script标签的代码就行了，但总觉得不规范……）：
{% highlight html linenos %}
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>The Target of Form</title>
    <script>
        alert(123);
    </script>
  </head>
  <body>
  </body>
</html>
{% endhighlight %}

**PS:** 当target指定的name不存在时（这个涉及到window.name属性了），那么会新开一个window.name为该name值的窗口。之后的每次提交都会到这个窗口来，如果关闭了此窗口，那么再次提交时还是会新开启窗口的。

**PPS:** 关于重复表单提交（点击刷新按钮），似乎对于新开窗口方式，无重复提交现象出现（感觉这种方式就是以POST形式新开窗口-_-）。而针对内嵌的iframe方式，则会有重复表单提交现象（IE8、Firefox如此，但Chrome除外），具体如何我就不进行测试了，这是题外话了，问题无处不在，有新的东西就有新的问题，有兴趣的朋友自己试试。

 

最后吐槽下，该[页面][page_cccx]的验证码是js生成的数字，不禁让我回想起去年写过的类似代码，只是这里的代码兼容性真是差呢，也就ie下能运行了。

 

**参考资料：**
>[为什么铁道部的网站叫12306？](http://zhidao.baidu.com/link?url=ER04KXHiSYmxCZ7ptQCbFYIDIpUFPlcG35xSVHxX-YchSYT4Mi7uU8jeeJsnhbg4ckm_W310ePpa-TC5y6G9Na)
>[铁路客户服务中心客服电话号码](http://www.12306.cn/mormhweb/tlkytst/)
>[HTML \<form\> 标签的 target 属性](http://www.w3school.com.cn/tags/att_form_target.asp)
>[HTML 5 \<form\> target 属性](http://www.w3school.com.cn/html5/att_form_target.asp)
>[xhtml1-transitional.dtd][xhtml_dtd_transitional]
>[xhtml1-strict.dtd][xhtml_dtd_strict]

[12306]:    http://www.12306.cn/
[票价查询]: http://dynamic.12306.cn/TrainQuery/ticketPriceByStation.jsp
[余票查询]: http://dynamic.12306.cn/otsquery/query/queryRemanentTicketAction.do?method=init
[html4_target]: http://www.w3school.com.cn/tags/att_form_target.asp
[html5_target]: http://www.w3school.com.cn/html5/att_form_target.asp
[xhtml_dtd_strict]: http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd
[xhtml_dtd_transitional]: http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd
[page_cccx]: http://dynamic.12306.cn/map_zwdcx/CCCX.jsp
