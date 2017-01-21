---
layout: post
date: 2014-01-12 21:02:00
title: "IE8突然显示不了PNG图片了……"
tags:
- IE
- PNG
- 图片
- IE8
---

### 唠叨

前段时间遇到了一件很郁闷的事情，刚开始没发觉，因为我很少用IE(8)，虽然很少用，但是很多软件内其实都是靠IE来访问页面的，比如迅雷(7，我用的版本有点低-_-)，在打开离线下载标签页时，就是通过IE来浏览在线离线下载页面的，当时碰到在搞活动，很心动，以为可以免费领取离线下载空间了（之前有过类似活动，不过刚好前段时间到期了），于是点击到活动页面，准备参与下，可是一直找不到“领取”之类的按钮，于是断定开没开始吧（可是看活动时间已经开始了啊……）。过了几天看了下还是如此，便好奇着想用Chrome来访问下看看，居然发现按钮了！好吧，没有太在意（以为IE已经无法“解析”迅雷页面了……）。但事情并没有就此结束，后来陆续发现IE8下无法显示部分图片了，之后便搜索了一吧，了解到了问题所在，如题！

<!--more-->

以前IE一有问题，都是重装的，可能当时吃饱了撑着，不过重装一遍感觉犹如焕然一新般，不过现在没这个闲功夫，因为IE重装可没有这么简单，不像普通的安装软件，它还需要重启电脑，类似于安装Windows更新补丁吧，比如IE的更新就是通过补丁的形式，重装IE则会重新安装所有补丁，所以等待的时间可想而知……

### 解决方法

既然网上能搜到这个问题，那么便有相应的解决方法了，我通过如下方法得以解决（本方法不一定适用于所有类似情况，毕竟具体原因可能各不相同，但值得一试~）。

新建文本文件，复制下面代码保存，然后把文件后缀改为.reg，双击运行来修改注册表来修复IE8的问题。（或者直接下载我建好的文件运行即可：[下载地址](./fix_ie8_png_error.reg)）

```
Windows Registry Editor Version 5.00
 
[HKEY_CLASSES_ROOT\MIME\Database\Content Type\image/png]
"Extension"=".png"
"Image Filter CLSID"="{A3CCEDF7-2DE2-11D0-86F4-00A0C913F750}"
 
[HKEY_CLASSES_ROOT\MIME\Database\Content Type\image/png\Bits]
"0"=hex:08,00,00,00,ff,ff,ff,ff,ff,ff,ff,ff,89,50,4e,47,0d,0a,1a,0a
```

以下是我参考的网上一篇[文章][ref1]的解决方法，其中操作到第二步时，我的情况是，覆盖文件前后，运行“`regsvr32 C:\WINDOWS\system32\pngfilt.dll`”时，都提示“已加载 `c:\windows\system32\pngfilt.dll`，但没有找到DllRegisterSever 输入点。无法注册这个文件”，对此我只能说，此处应该没有问题啊！至少在我电脑上是如此，因此我就执行了第三步而已。

>  IE版本:IE8
>
>  1. 查看C:\WINDOWS\system32 目录下是否有pngfilt.dll文件，如没有请点击以下地址下载
IE 8 pngfilt.dll[下载地址][ref2] （说明此文件适用与IE8，其他IE版本使用可能会出现未知问题）
>
>  2. 注册pngfilt.dll组件，在“开始”-“运行”-输入“regsvr32 C:\WINDOWS\system32\pngfilt.dll”.如果出现“ 已加载 c:\windows\system32\pngfilt.dll，但没有找到DllRegisterSever 输入点。无法注册这个文件”，则表明这个文件可能损坏了 ”，请点击以上下载地址下载，覆盖后请在运行一次。我处理到这时IE能显示出PNG图片了。
>
>  3. 网上很多都是说因为注册表问题导致IE不显示PNG图片。以下是代码，复制保存为.reg文件，双击即可导入，请在导入前备份你的注册表。
>
> ```
>	Windows Registry Editor Version 5.00
>	 
>	[HKEY_CLASSES_ROOT\MIME\Database\Content Type\image/png]
>	"Extension"=".png"
>	"Image Filter CLSID"="{A3CCEDF7-2DE2-11D0-86F4-00A0C913F750}"
>	 
>	[HKEY_CLASSES_ROOT\MIME\Database\Content Type\image/png\Bits]
>	"0"=hex:08,00,00,00,ff,ff,ff,ff,ff,ff,ff,ff,89,50,4e,47,0d,0a,1a,0a
> ```


### 参考资料
[解决IE8不显示 PNG图片的方法][ref1]


[ref1]: http://www.czj.name/archives/265.so "解决IE8不显示 PNG图片的方法"
[ref2]: http://czjtuku.bcs.duapp.com/soft/pngfilt.rar