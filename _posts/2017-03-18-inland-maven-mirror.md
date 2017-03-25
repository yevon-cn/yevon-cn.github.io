---
layout: post
date: 2017-03-18 16 +0800
title: "国内Maven镜像"
header-color: "black"
tags:
- Maven
- 镜像
- Gradle
---

在国内访问国外资源一直是一个折腾人的问题，譬如使用Maven，既然存在问题，那么可能就存在解决方案，没错，应该是有国内镜像，只是不知道镜像会不会一直维护下去。最近发现有阿里云的maven仓库，甚是激动，在此做个记录。

<!--more-->

# maven配置

用maven的话，在setting.xml中的mirrors里，添加阿里云的maven mirror即可，不做详细介绍，地址如下所示。

```xml
<mirror>
  <id>alimaven</id>
  <name>aliyun maven</name>
  <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
  <mirrorOf>central</mirrorOf>        
</mirror>
```

# gradle配置

用gradle的话，在build.gradle中的repositories里，添加阿里云的maven mirror即可，配置如下所示。


```js
maven {
    url "http://maven.aliyun.com/nexus/content/groups/public/"      
}
```

# 总结

目前还没怎么实际用过，不过至少可以体验到飞一般的速度了吧~
