---
layout: post
date: 2017-03-09 晚上
title: "设置启动Eclipse的JDK"
header-color: "#2c2255"
tags:
- Eclipse
---

随着Eclipse版本更新，避免不了需要更新JDK，但是因为各种原因仍然需要使用多个版本的JDK，而Eclipse默认使用的是系统环境变量中配置的JDK，当Eclipse不支持该JDK时就显得尴尬了，所以这时候需要自己来指定JDK了。

<!--more-->

网上搜了下，发现有几种配置方式，但没有说清楚，所以自己研究了下，来做个总结。

指定JDK需要修改Eclipse根目录下的eclipse.ini文件，通过在头部(或者`-vmargs`参数前)添加`-vm`参数来指定JDK(或JRE)的bin路径，如指定路径`D:/Java/jdk1.8.0_11/bin`，如下所示，注意格式。

```txt
-vm
D:/Java/jdk1.8.0_11/bin
-startup
plugins/org.eclipse.equinox.launcher_1.3.201.v20161025-1711.jar
--launcher.library
plugins/org.eclipse.equinox.launcher.win32.win32.x86_64_1.1.401.v20161122-1740
......
```

如果`-vm`的路径配置错误的话，启动Eclipse时也许会看到如下错误信息，根据该提示信息可知，Eclipse是扫描路径下的`default.ee`、`javaw.exe`、`jvm.dll`文件，借此来启动Eclipse的，因此另外几种配置方式就是将路径指定到这几个文件中的其中一个，但经测试并不能完全保证能启动，也许32位的Eclipse可以但64位的不行，所以配置到bin目录最佳。

```txt
A Java Runtime Environment (JRE) or Java Development Kit (JDK)
must be available in order to run Eclipse. No Java virtual machine
was found after searching the following locations:
D:/Java/jdk1.8.0_11\default.ee
D:/Java/jdk1.8.0_11\javaw.exe
D:/Java/jdk1.8.0_11\jvm.dll
```

另外，64位的Eclipse必须用64位的JDK，如果用32位的则无法启动的，启动Eclipse时可能会提示如下错误信息，根据该提示信息可知，配置bin目录的情况下，Eclipse也许是去寻找`jvm.dll`文件了，当然也有可能是挨个找的，最后轮到了`jvm.dll`。

```txt
Failed to load the JNI shared library "D:\Java\jdk1.8.0_102\bin\..\jre\bin\client\jvm.dll".
```

以上信息是在Windows下的测试结果和总结，其它系统应该大致相同，仅做参考！
