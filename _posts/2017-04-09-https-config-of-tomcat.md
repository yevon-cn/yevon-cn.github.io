---
layout: post
date: 2017-04-09 +0800
title: "Tomcat 8.5 配置 HTTPS"
header-color: "#d2a41f"
tags:
- Tomcat
- HTTPS
---

一直没机会了解和使用 `HTTPS`，这不前不久，微信小程序开放了个人开发者的申请注册，让我可以玩一下了，然而却发现小程序对服务端请求必须是 `HTTPS`，当然这确实没啥奇怪的，这样安全才有保障。于是借此机会学习了下 `Tomcat` 的 `HTTPS` 配置。

<!--more-->

因为不同版本 `Tomcat` 配置上可能有所不同，所以这里只介绍最新的 8.5 版本。另外配置 `HTTPS` 需要申请证书，这里不做介绍。


# 开启 HTTPS 访问

打开 conf 下的 `server.xml`，从中找到如下注释信息。

```xml
<!-- Define a SSL/TLS HTTP/1.1 Connector on port 8443
     This connector uses the NIO implementation. The default
     SSLImplementation will depend on the presence of the APR/native
     library and the useOpenSSL attribute of the
     AprLifecycleListener.
     Either JSSE or OpenSSL style configuration may be used regardless of
     the SSLImplementation selected. JSSE style configuration is used below.
-->
<!--
<Connector port="8443" protocol="org.apache.coyote.http11.Http11NioProtocol"
           maxThreads="150" SSLEnabled="true">
    <SSLHostConfig>
        <Certificate certificateKeystoreFile="conf/localhost-rsa.jks"
                     type="RSA" />
    </SSLHostConfig>
</Connector>
-->
```

去掉 `Connector` 的注释，修改 `SSLHostConfig` 为如下格式（PS：旧版本的 `Tomcat` 是直接配置在 `Connector` 属性上的，该写法以后将被弃用）。如果是更复杂的需求，则需要根据实际情况并参考官方文档来进行配置，这里不做深入研究。

```xml
<Connector port="8443" protocol="org.apache.coyote.http11.Http11NioProtocol"
           maxThreads="150" SSLEnabled="true">
    <SSLHostConfig>
        <Certificate certificateKeystoreFile="conf/域名的 jks 文件"
                     certificateKeystorePassword="jks 文件密码"
                     certificateKeyAlias="jks 别名，一般为申请的证书域名"
                     type="RSA" />
    </SSLHostConfig>
</Connector>
```

参数说明：

1. `certificateKeystoreFile` 指定 jks 文件所在，相对路径则是相对于 `$CATALINA_BASE`，一般为 `Tomcat` 所在目录。
2. `certificateKeystorePassword` jks 文件密码。
3. `certificateKeyAlias` jks 别名,一般为申请的证书域名，可通过 jdk 的 `keytool –list –keystore jks文件 –storepass jks文件密码`命令查看 jks 别名。

至此配置后，则能通过 `HTTPS` 访问了（默认端口为 8443），但同时也可以通过 `HTTP` 访问（默认端口为 8080）。


# 强制 HTTPS 访问

为了让网站强制使用 `HTTPS`，需要修改 `Tomcat` conf 目录下 `web.xml`，在文件末尾（一般情况）的 `</web-app>` 结束标签前添加如下代码。

```xml
<login-config>    
    <auth-method>CLIENT-CERT</auth-method>
    <realm-name>Client Cert Users-only Area</realm-name>
</login-config> 
<security-constraint>
  <web-resource-collection>
      <web-resource-name>SSL</web-resource-name>
      <url-pattern>/*</url-pattern>
  </web-resource-collection>
  <user-data-constraint>
      <transport-guarantee>CONFIDENTIAL</transport-guarantee>
  </user-data-constraint>
</security-constraint>
```

该配置是我网上搜寻来的，用是可以用的，只是尚未完全理解。

其中 `login-config` 好像是配置 `Tomcat` users 登录认证方式为客户端证书认证，这个一般很少用到，具体如何认证没研究过，只是似乎和 `HTTPS` 没啥关系，疑似无用配置；`security-constraint` 是配置所有url请求为 `HTTPS`，这个配置才是主要的。

配置完后，通过 `HTTP` 访问的请求自动会重定向到 `HTTPS`。

需要注意的是，如果在部署项目时，如果项目的 `web.xml` 配置了 `security-constraint` 相关参数，则可能会覆盖掉在 `Tomcat` 的 `web.xml` 里配置的信息，导致该项目部分地址可通过`HTTP`访问。


# HTTPS 默认端口

一般访问网站，不管是 `HTTP` 还是 `HTTPS` ，都不需要加端口号的，因为 `HTTP` 默认是 80，`HTTPS` 默认是 443，而在 `Tomcat` 中则是 8080 和 8443，为了访问方便、简洁，则需要修改 conf 下的 `server.xml`，将里面的 8080 都改成 80，8443 都改成 443 即可。


# 总结

本文仅仅是简单介绍了下 `Tomcat` 配置 `HTTPS` ，对其有一个基本的了解，对于一些配置仍有了解不足的，因此实际运用过程中，仍需要参考官方文档或 Google 来解决问题。


# 参考资料

* [tomcat 8.5 以上版本的证书部署](https://www.trustasia.com/java-tomcat8-install)
* [SSL/TLS Configuration HOW-TO](https://tomcat.apache.org/tomcat-8.5-doc/ssl-howto.html)
* [The HTTP Connector#SSL Support](https://tomcat.apache.org/tomcat-8.5-doc/config/http.html#SSL_Support)
* [强制使用 HTTPS --- Tomcat 篇](http://blog.csdn.net/xiaobin_hlj80/article/details/6003355)
* [Tomcat 容器管理安全的几种验证方式](https://my.oschina.net/itblog/blog/678845?fromerr=mnnjJA0O)