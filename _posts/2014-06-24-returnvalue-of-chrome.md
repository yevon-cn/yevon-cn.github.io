---
layout: post
date: 2014-06-24 20:40:00
title: "returnValue of Chrome"
tags:
- JavaScript
- returnValue
- showModalDialog
- Chrome
---

说实话，我一看到这个`returnValue`就有点反感，感觉这个就是IE式的老套的用法，因为项目中有用到就了解了下，以下主要是一些我的理解和发现吧。

<!--more-->

**PS：**`returnValue`是`window`的属性，`showModalDialog`和`open`是`window`的方法。

`returnValue`是与`showModalDialog`搭配使用的，`showModalDialog`用于打开窗口，与`open`类似效果，但通过`showModalDialog`打开窗口时有如下特点：

1. 打开窗口后，将不能再操作父窗口了(正常情况下，父窗口将获取不到焦点了)；
2. 确切的说父窗口在执行`showModalDialog`这一步时停止了，等待子窗口操作返回，而`showModalDialog`之后的js代码也就暂时不会执行了；
3. 而`returnValue`就是返回值，子窗口通过`window.returnValue`将操作结果返回给父窗口，在子窗口调用`window.close`方法后，`returnValue`将作为`showModalDialog`的返回值传递到父窗口中，之后继续执行`showModalDialog`后面的js代码；

网上的搜罗了下，都说IE、Firefox支持的，而Chrome虽然有`showModalDialog`方法，但效果仅仅类似`open`，且不支持`returnValue`，至于Opera则完全不支持(应该说的不是Webkit内核的Opera)。

初步写代码测试了下(只测IE、Firefox、Chrome)，我写了三个页面，主要逻辑是：t1中打开t2、t2中打开t3、同时每个页面上添加点击测试、刷新测试按钮(用于Chrome测试)，基本html代码如下，可直接点击页面地址进行浏览：

##### [t1.html]({{site.rsurl}}/2014/06-24-t1.html) 代码：
```html
<!doctype html>
<html>
	<meta charset="utf-8"/>
	<title>t1</title>
	<script>
		alert("load t1");
		function openT2(){
			alert(showModalDialog('t2.html',{msg:"arguments from t1.html",win:window},''));
			alert('子窗口已关闭');
		}
		function openT1Self(){
			alert(showModalDialog('t1.html',{msg:"arguments from t1.html",win:window},''));
			alert('子窗口已关闭');
		}
		function closeSelf(){
			window.returnValue='从t1正常返回的returnValue';
			window.close();
		}
		if(window.dialogArguments){
			if(window.dialogArguments.msg){
				alert(window.dialogArguments.msg);
			}else{
				alert(window.dialogArguments);
			}
		}
		function reloadPage(){
			alert('before');
			window.location.reload();
			alert('after');
		}
		function setReturnValue(){
			var str = prompt("请输入returnValue值")
			window.returnValue = "t1在刷新前所赋的returnValue："+str;
			alert("赋值完毕，请点击刷新！");
		}
	</script>
	<body>
		<a href="javascript:openT2();">打开t2</a>
		<a href="javascript:openT1Self();">打开t1自己</a>
		<a href="javascript:closeSelf();">关闭</a>
		<a href="javascript:alert('点击了');">Chrome下点击测试</a>
		<a href="javascript:setReturnValue();">在刷新前给returnValue赋值</a>
		<a href="javascript:reloadPage();">点击刷新</a>
	</body>
</html>
```

##### [t2.html]({{site.rsurl}}/2014/06-24-t2.html) 代码：
```html
<!doctype html>
<html>
	<meta charset="utf-8"/>
	<title>t2</title>
	<script>
		alert("load t2");
		function openT3(){
			alert(showModalDialog('t3.html',{msg:"arguments from t2.html",win:window},''));
			alert('子窗口已关闭');
		}
		function closeSelf(){
			window.returnValue='从t2正常返回的returnValue';
			window.close();
		}
		if(window.dialogArguments){
			if(window.dialogArguments.msg){
				alert(window.dialogArguments.msg);
			}else{
				alert(window.dialogArguments);
			}
		}
		function reloadPage(){
			alert('before');
			window.location.reload();
			alert('after');
		}
		function setReturnValue(){
			var str = prompt("请输入returnValue值")
			window.returnValue = "t2在刷新前所赋的returnValue："+str;
			alert("赋值完毕，请点击刷新！");
		}
	</script>
	<body>
		<a href="javascript:openT3();">打开t3</a>
		<a href="javascript:closeSelf();">关闭</a>
		<a href="javascript:alert('点击了');">Chrome下点击测试</a>
		<a href="javascript:setReturnValue();">在刷新前给returnValue赋值</a>
		<a href="javascript:reloadPage();">点击刷新</a>
	</body>
</html>
```

##### [t3.html]({{site.rsurl}}/2014/06-24-t3.html) 代码：
```html
<!doctype html>
<html>
	<meta charset="utf-8"/>
	<title>t3</title>
	<script>
		alert("load t3");
		function closeSelf(){
			window.returnValue='从t3正常返回的returnValue';
			window.close();
		}
		if(window.dialogArguments){
			if(window.dialogArguments.msg){
				alert(window.dialogArguments.msg);
			}else{
				alert(window.dialogArguments);
			}
		}
		function reloadPage(){
			alert('before');
			window.location.reload();
			alert('after');
		}
		function setReturnValue(){
			var str = prompt("请输入returnValue值")
			window.returnValue = "t3在刷新前所赋的returnValue："+str;
			alert("赋值完毕，请点击刷新！");
		}
	</script>
	<body>
		<a href="javascript:closeSelf();">关闭</a>
		<a href="javascript:setReturnValue();">在刷新前给returnValue赋值</a>
		<a href="javascript:reloadPage();">点击刷新</a>
	</body>
</html>
```

##### 测试1
直接将t1.html文件拖入浏览器浏览进行测试。

###### 测试结果：
* IE、Firefox没有问题；
* Chrome下则`returnValue`几乎无效，具体细节：
	1. `showModalDialog`效果接近`open`，即只是打开了一个新窗口而已，父窗口仍然可以操作，而`showModalDialog`后的语句没有执行，但控制台上却有些信息提示，有一个警告(灰色)和错误(红色)：

		> <span style="color:gray;">Chromium is considering deprecating showModalDialog. Please use window.open and postMessage instead.</span>
		> <span style="color:#e55;">Uncaught SecurityError: Blocked a frame with origin "null" from accessing a frame with origin "null". Protocols, domains, and ports must match. </span>

		警告信息表明Chromium将弃用`showModalDialog`(经验证，在Chromium38.0.2074.0中，`showModalDialog`已报`undefined`错误，看来已被遗弃，见官方[Issue345831][]，不知Chrome之后会不会如此……)。

		错误信息是在关闭子窗口后出来的，这个就是说明了为什么`returnValue`无效以及`showModalDialog`后面的语句为什么没执行(报错了么,呵呵)，这个错误似乎跟跨域访问有点类似啊。

		**PS：**在较老版本的Chrome中并不会有这里的警告及错误信息，故可能让人误以为不支持`returnValue`
	2. 当t1.html通过`showModalDialog`打开t1.html(即自己)的时候，`returnValue`竟起作用了，但在子窗口打开的情况下父窗口仍然可以操作，不同的是的`showModalDialog`后面的语句在关闭子窗口后执行了，说明此时没有1中的那个错误了；
	3. 但如果子页面刷新了，则无论之后给`returnValue`赋什么值，最终`showModalDialog`的返回值将一直是子页面第一次刷新前给`returnValue`所赋的值，如果第一次刷新前没有给`returnValue`赋值，则将是`undefined`，这说明子页面在刷新后与父页面失去了联系，导致`returnValue`失效；
	4. 在子窗口打开情况下，虽然父窗口能够操作，但在父窗口中点击“点击刷新”后两个`alert`都执行了，而页面却没有刷新，说明有些特殊操作如`window.location.reload();`将会等到子窗口关闭后才会执行。


通过测试1得知在chrome下本地直接浏览网页的形式使用`showModalDialog`存在类似跨域访问的错误，因此需要将网页放到Web应用服务器上测试，至于子页面刷新导致的问题也许就那样了，算是个bug吧。

##### 测试2
将三个页面放到Web应用服务器(Tomcat)上，然后打开t1.html浏览进行测试。

###### 测试结果：
* IE、Firefox没有问题；
* Chrome下则`returnValue`基本有效，具体细节：
	1. 正常操作情况下，能够通过`returnValue`返回数据，`showModalDialog`后面的代码也能在关闭子窗口后执行；
	2. 但如果子页面刷新了，则无论之后给`returnValue`赋什么值，最终`showModalDialog`的返回值将一直是子页面第一次刷新前给`returnValue`所赋的值，如果第一次刷新前没有给`returnValue`赋值，则将是`undefined`，这说明子页面在刷新后与父页面失去了联系，导致`returnValue`失效；
	3. 在子窗口打开情况下，虽然父窗口能够操作，但在父窗口中点击“点击刷新”后两个`alert`都执行了，而页面却没有刷新，说明有些特殊操作如`window.location.reload();`将会等到子窗口关闭后才会执行。


### 总结

鉴于网上发表的关于Chrome之`returnValue`的文章都是几年前的，也许那时确实是完全不支持`returnValue`吧，不过我拿了个较老的版本(v11)测过，结果大致跟新版一样。

但Chrome虽然支持`returnValue`，但使用体验却不怎么样，有以下几点：

1. 打开子窗口情况下，还是能够操作父窗口；
2. 子窗口在刷新后，`returnValue`就失效了(`window.dialogArguments`也变为`undefined`了)，只能返回第一次刷新前的`returnValue`(该问题可通`window.opener`传参解决，该属性指向父窗口，且不会因为刷新导致失效，具体请参考[ref1][]文中的“解决returnValue问题”小节)；

于是乎可以说Chrome不支持`returnValue`吧，在之后的版本中`showModalDialog`这个方法应该会被移除了，所以说这个没有什么意义了啊。

如果一定要继续用`showModalDialog`的话，可以考虑在子窗口中通过`window.opener`与父窗口进行传参来代替`returnValue`的方式，而用`open`来代替`showModalDialog`，而在子窗口打开情况下，可以在父窗口中覆盖一层div来禁用用户操作等等，还是可以将就着模拟`showModalDialog`的效果的，但要完全模拟，有点困难啊。

### 参考资料

* [Chrome不支持showModalDialog模态对话框和无法返回returnValue的问题][ref1]


[Issue345831]: https://code.google.com/p/chromium/issues/detail?id=345831
[ref1]: http://www.cnblogs.com/chopper/archive/2012/06/25/2556266.html "Chrome不支持showModalDialog模态对话框和无法返回returnValue的问题"