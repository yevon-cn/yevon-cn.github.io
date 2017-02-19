---
layout: post
title: "关于Tomcat之response.encodeURL的实现疑问和发现"
date: 2013-03-07 17:55:32
tags: 
- Java
- Tomcat
- response
---

### 1、好奇

一直很好奇为什么`response.encodeURL`能够在cookie失效的时候在url上生成加上`sessionid`以便于正常服务的正常运作，在网上找了相关资料未果，因此就有了这次的探索和发现。

<!--more-->

### 2、猜测

我的初步的看法是：服务端一定能够判断浏览器是否禁用了cookie。可是如果这是真的，那么首先一定是浏览器以某种方式告诉了服务器，而服务器也因此能够进行判断。可是如果这是真的话，为什么没有方法给我们开发人员调用来判断客户端是否禁用了cookie呢？无奈之下只能进行测试了。

### 3、验证猜测

如果服务器能获取到浏览器cookie是否禁用的信息的话，那一定是通过http协议相关信息吧，因此我比较了下在cookie禁用的情况下，传输的http请求信息是否相同。

结果：完全相同。（没啥可帖的，反正就是一样啦！）

### 4、瞧源码

无奈无果，在过了n天后，我好奇心又起，于是只好去看看`response.encodeURL`方法的源码了，对了，我用的是tomcat，所以要去看tomcat对`response`的具体实现了，由于本人还是比较懒得，所以不想去网上下源代码了，就选择了用反编译工具来查看我的tomcat中的jar包（我用的[是](http://java.decompiler.free.fr/?q=jdgui)）。

首先需要知道`response`的实现类是什么，这个就用到了反射了，一句话搞定：`response.getClass().getName()`，得：`org.apache.catalina.connector.ResponseFacade`。

然后就去找tomcat中的jar包了，在lib目录下，更具`request`的包名确认是catalina.jar。

最后查看`encodeURL`方法，在`ResponseFacade`中是调用了`org.apache.catalina.connector.Response`的`encodeURL`方法，最后发现了个人认为的关键之处：

```java
if (hreq.isRequestedSessionIdFromCookie()) {
  return false;
}
```

即它是通过`request`的`isRequestedSessionIdFromCookie`方法来判断`sessionid`是否从cookie获得，如果获得了那么就表明未禁用，反之则禁用了。

### 5、再次疑惑

如果就凭借`isRequestedSessionIdFromCookie`这个方法来判断，个人感觉不怎么好使。比如当我第一次访问的时候，客户端还没cookie呢，如何来的`sessionid`，那岂不是即便未禁用cookie，`encodeUR`L方法也给url加上了sessionid标识？

### 6、再次验证

于是我在未禁用cookie的浏览器中首次访问，查看`encodeURL`方法是否给url加了sessionid，最终发现确实如此。在第一次访问过后，再次访问就没加`sessionid`了，因为第一次访问时，在客户端cookie中写入了`sessionid`。

### 7、结束语

原来是这样啊，在了解了底层代码后，我便对其有了更进一步的了解，有时候光测试是很难得出结论来的。