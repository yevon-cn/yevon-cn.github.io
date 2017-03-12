---
layout: post
date: 2017-03-12 17:00 +0800
title: "Windows下安装Gradle"
header-color: "#02303A"
tags:
- Windows
- Gradle
---

最近想在Windows下玩Gradle，根据Gradle官网的[安装说明][gradle_install]介绍，有两种安装方法，一种是手动安装，一种是通过包管理程序来安装，在此记录下这两种安装方式。

<!--more-->

# 题外话：Gradle怎么读？

Gradle在字典里查不到，所以该怎么读呢？网上查了下，参考了下[知乎](https://www.zhihu.com/question/35009427)、[how2read.me](http://how2read.me/questions/Gradle)的介绍，copy了知乎上[图腾helloword](https://www.zhihu.com/people/min-yu-qiang)的回答。

```txt
英文原文：gradle
英式音标：[ɡreɪdl] 
美式音标：[ɡredl]
```

# 手动安装

1.从官网下载最新的Gradle程序（[地址](https://gradle.org/releases)），解压到要安装的路径下。

2.将Gradle根目录下的bin路径配置到系统环境变量即可。


# 用包管理程序安装

1.安装Chocolatey，详见Chocolatey官网的[安装说明][chocolatey_install]或我之前写的文章。

2.在cmd中通过命令`choco install gradle`来安装Gradle，运行结果如下所示，由于资源是从国外下载的，有可能因网络问题导致下载失败或着超时等结果，多试几次就行了。更新的话，则用命令`choco upgrade gradle`。

```txt
C:\WINDOWS\system32>choco install gradle
Chocolatey v0.10.3
Installing the following packages:
gradle
By installing you accept licenses for the packages.

gradle v3.1 [Approved]
gradle package files install completed. Performing other installation steps.
The package gradle wants to run 'chocolateyinstall.ps1'.
Note: If you don't run this script, the installation will fail.
Note: To confirm automatically next time, use '-y' or consider setting
 'allowGlobalConfirmation'. Run 'choco feature -h' for more details.
Do you want to run the script?([Y]es/[N]o/[P]rint): y

WARNING: This package has only been tested with Chocolatey 0.9.9
File appears to be downloaded already. Verifying with package checksum to determine if it needs to be redownloaded.
Error - hashes do not match. Actual value was '2A1CD48929C307077A0E343CA4B34EC640D2FE4B8F9B3804723A6CE3FB4F5C63'.
Downloading gradle
  from 'https://services.gradle.org/distributions/gradle-3.1-bin.zip'
Progress: 100% - Completed download of C:\Users\Admin\AppData\Local\Temp\chocolatey\gradle\3.1\gradle-3.1-bin.zip (65.77 MB).
Download of gradle-3.1-bin.zip (65.77 MB) completed.
Hashes match.
Extracting C:\Users\Admin\AppData\Local\Temp\chocolatey\gradle\3.1\gradle-3.1-bin.zip to C:\ProgramData\chocolatey\lib\gradle\tools...
C:\ProgramData\chocolatey\lib\gradle\tools
Added C:\ProgramData\chocolatey\bin\gradle.exe shim pointed to '..\lib\gradle\tools\gradle-3.1\bin\gradle.bat'.
Environment Vars (like PATH) have changed. Close/reopen your shell to
 see the changes (or in powershell/cmd.exe just type `refreshenv`).
 The install of gradle was successful.
  Software installed to 'C:\ProgramData\chocolatey\lib\gradle\tools'

Chocolatey installed 1/1 packages. 0 packages failed.
 See the log for details (C:\ProgramData\chocolatey\logs\chocolatey.log).
```

# 验证安装是否成功

可通过命令`gradle -v`验证是否已安装并查看当前的版本信息。


# 总结

还是手动安装方便灵活，而且发现通过Chocolatey安装的版本才`3.1`，Gradle官网最新的是`v3.4.1`了，只能说通过包管理程序安装的话，很难保证程序是最新版的，还是老老实实手动安装吧。

[gradle_install]: https://gradle.org/install
[chocolatey_install]: https://chocolatey.org/install