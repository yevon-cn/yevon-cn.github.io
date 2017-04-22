---
layout: post
date: 2017-04-22 +0800
title: "免 log in 使用 SourceTree"
header-color: "#205081"
tags:
- SourceTree
- Git
---

SourceTree 是 Mac 和 Windows 下免费的 Git 和 Hg 客户端管理工具，正因为免费，所以安装时需要注册账号登录后才能使用，但鉴于 it is not made in China，so 简单的注册、登录就变得不简单了，于是找了个简单的方法，跳过该步骤，免去不必要的麻烦，由于没有 Mac 的环境，故该方法仅适用于 Windows，Mac 下仅做参考。

PS：此方法仅供不时之需，如果有条件，那么注册、登录下也无妨，算是对 SourceTree 的支持吧！

<!--more-->

系统环境：Win 10，不同 Windows 系统路径上可能稍有不同。

SourceTree 版本：1.9.10。

按正常步骤安装、初始化后，会在`C:\Users\当前用户\AppData\Local\Atlassian\SourceTree`，即`%LocalAppData%\Atlassian\SourceTree`下产生`accounts.json`文件，该文件内容如下，其中 `Username` 值为安装时登录的邮箱（其它参数作用不详），在安装完后可随意修改，或者和 `Email` 一样设置为`null`，修改后暂未发现异常。

```json
[
  {
    "$id": "1",
    "$type": "SourceTree.Api.Host.Identity.Model.IdentityAccount, SourceTree.Api.Host.Identity",
    "Authenticate": true,
    "HostInstance": {
      "$id": "2",
      "$type": "SourceTree.Host.Atlassianaccount.AtlassianAccountInstance, SourceTree.Host.AtlassianAccount",
      "Host": {
        "$id": "3",
        "$type": "SourceTree.Host.Atlassianaccount.AtlassianAccountHost, SourceTree.Host.AtlassianAccount",
        "Id": "atlassian account"
      },
      "BaseUrl": "https://id.atlassian.com/"
    },
    "Credentials": {
      "$id": "4",
      "$type": "SourceTree.Model.BasicAuthCredentials, SourceTree.Api.Account",
      "Username": "注册的邮箱",
      "Email": null
    },
    "IsDefault": false
  }
]
```

观察该文件内容可知，SourceTree 应是根据该文件信息判断是否已登录其账号，所以只要在安装完 SourceTree 后，手动创建该文件，填入上述信息（请根据实际情况做修改），便可欺骗 SourceTree，使其认为已登录账号。

需要注意的是，在打开 SourceTree 的情况下创建该文件是没效果的！


虽说 SourceTree 真的不怎么好用，至少 Windows 下是如此，但庆幸的是终于可以免去 log in 这步麻烦的操作了，可见其对该步骤要求不高，毕竟是免费的，所以可以简单的通过修改文件跳过。

PS：之前在网上查的资料都有提到安装 SourceTree 后需要导入 sourcetree.license，但一直未发现有该步骤，猜测可能是老版本或者 Mac 版才需要的操作。