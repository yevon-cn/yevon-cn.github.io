---
layout: post
date: 2017-03-12 11:00 +0800
title: "安装Chocolatey"
header-color: "#2f6492"
tags:
- Windows
- Chocolatey
---

最近想在Windows下玩Gradle，根据Gradle官网的[安装说明][gradle_install]介绍，有两种安装方法，一种是手动安装，相对来说简单点，另一种是通过包管理程序来安装，这就需要借助Chocolatey来安装Gradle了。因为对Chocolatey有点兴趣，所以打算折腾下了，在此记录下Chocolatey的安装过程。

<!--more-->

Chocolatey是Windows下一款命令行包管理软件，简单说这就是Windows的apt-get，可以通过命令来安装、更新、卸载程序。安装Chocolatey，对系统也是有要求的，如下所示，当然如果你条件没满足，只能说你OUT了...

```txt
Windows 7+ / Windows Server 2003+
PowerShell v2+
.NET Framework 4+
```

根据Chocolatey官网的[安装说明][chocolatey_install]介绍，只需在PowerShell v3+ **(以管理员身份运行)**中执行一条命令便可完成Chocolatey的安装了，如下所示，但鉴于是国外的东东，那只能祈求上帝保佑了。

```powershell
iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
```

在PowerShell中执行该命令后提示如下错误信息。

```txt
Installing chocolatey on this machine
& : 无法加载文件 C:\Users\Admin\AppData\Local\Temp\chocolatey\chocInstall\tools\chocolateyInstall.ps1，因为在此系统上
禁止运行脚本。有关详细信息，请参阅 http://go.microsoft.com/fwlink/?LinkID=135170 中的 about_Execution_Policies。
所在位置 行:245 字符: 3
+ & $chocInstallPS1
+   ~~~~~~~~~~~~~~~
    + CategoryInfo          : SecurityError: (:) []，PSSecurityException
    + FullyQualifiedErrorId : UnauthorizedAccess
```

目测应该是出于安全考虑，禁止脚本运行，其实这个在官网介绍中有提到，只不过我没怎么在意而已，根据微软的[文档](https://technet.microsoft.com/zh-CN/library/hh847748.aspx)介绍，PowerShell执行策略默认是`RESTRICTED`，即只允许单独的命令，不会运行脚本，所以需要通过命令来修改执行策略，可以修改为`REMOTESIGNED`，即要求从Internet下载的脚本等具有受信任的发布者的数字签名才能运行。查看、修改执行策略的命令如下所示。

```txt
PS C:\WINDOWS\system32> get-executionpolicy
Restricted
PS C:\WINDOWS\system32> set-executionpolicy remotesigned
执行策略更改
执行策略可帮助你防止执行不信任的脚本。更改执行策略可能会产生安全风险，如 http://go.microsoft.com/fwlink/?LinkID=135170
中的 about_Execution_Policies 帮助主题所述。是否要更改执行策略?
[Y] 是(Y)  [A] 全是(A)  [N] 否(N)  [L] 全否(L)  [S] 暂停(S)  [?] 帮助 (默认值为“N”): y
PS C:\WINDOWS\system32> get-executionpolicy
RemoteSigned
```

修改完后再次运行Chocolatey安装命令，提示如下错误信息。

```txt
PS C:\WINDOWS\system32> iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
Getting latest version of the Chocolatey package for download.
Getting Chocolatey from https://chocolatey.org/api/v2/package/chocolatey/0.10.3.
使用“2”个参数调用“DownloadFile”时发生异常:“在 WebClient 请求期间发生异常。”
所在位置 行:163 字符: 3
+   $downloader.DownloadFile($url, $file)
+   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [], MethodInvocationException
    + FullyQualifiedErrorId : WebException

Downloading 7-Zip commandline tool prior to extraction.
Extracting C:\Users\Admin\AppData\Local\Temp\chocolatey\chocInstall\chocolatey.zip to C:\Users\Admin\AppData\Local\Temp\chocolatey\chocInstall...
Unable to unzip package using 7zip. Perhaps try setting $env:chocolateyUseWindowsCompression = 'true' and call install
again. Error: 7-Zip encountered a fatal error while extracting the files
所在位置 行:219 字符: 9
+     2 { throw "$errorMessage 7-Zip encountered a fatal error while ex ...
+         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : OperationStopped: (Unable to unzip...cting the files:String) [], RuntimeException
    + FullyQualifiedErrorId : Unable to unzip package using 7zip. Perhaps try setting $env:chocolateyUseWindowsCompres
   sion = 'true' and call install again. Error: 7-Zip encountered a fatal error while extracting the files

```

第一个错误暂时忽略，第二个错误似乎是无法解压的问题，根据错误提示执行如下命令，使Chocolatey使用系统的压缩功能。

```powershell
$env:chocolateyUseWindowsCompression = 'true'
```

最后再次执行Chocolatey安装命令，提示如下。

```txt
PS C:\WINDOWS\system32> iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
Getting latest version of the Chocolatey package for download.
Getting Chocolatey from https://chocolatey.org/api/v2/package/chocolatey/0.10.3.
使用“2”个参数调用“DownloadFile”时发生异常:“在 WebClient 请求期间发生异常。”
所在位置 行:163 字符: 3
+   $downloader.DownloadFile($url, $file)
+   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [], MethodInvocationException
    + FullyQualifiedErrorId : WebException

Using built-in compression to unzip
Extracting C:\Users\Admin\AppData\Local\Temp\chocolatey\chocInstall\chocolatey.zip to C:\Users\Admin\AppData\Local\Temp\chocolatey\chocInstall...
Expand-Archive : 路径“C:\Users\Admin\AppData\Local\Temp\chocolatey\chocInstall\chocolatey.zip”不存在，或者不是有效的
文件系统路径。
所在位置 行:236 字符: 5
+     Expand-Archive -Path "$file" -DestinationPath "$tempDir" -Force
+     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (C:\Users\Admin...\chocolatey.zip:String) [Expand-Archive]，InvalidOperationException
    + FullyQualifiedErrorId : ArchiveCmdletPathNotFound,Expand-Archive

Installing chocolatey on this machine
Creating ChocolateyInstall as an environment variable (targeting 'Machine')
  Setting ChocolateyInstall to 'C:\ProgramData\chocolatey'
WARNING: It's very likely you will need to close and reopen your shell
  before you can use choco.
Restricting write permissions to Administrators
We are setting up the Chocolatey package repository.
The packages themselves go to 'C:\ProgramData\chocolatey\lib'
  (i.e. C:\ProgramData\chocolatey\lib\yourPackageName).
A shim file for the command line goes to 'C:\ProgramData\chocolatey\bin'
  and points to an executable in 'C:\ProgramData\chocolatey\lib\yourPackageName'.

Creating Chocolatey folders if they do not already exist.

WARNING: You can safely ignore errors related to missing log files when
  upgrading from a version of Chocolatey less than 0.9.9.
  'Batch file could not be found' is also safe to ignore.
  'The system cannot find the file specified' - also safe.
PATH environment variable does not have C:\ProgramData\chocolatey\bin in it. Adding...
警告: Not setting tab completion: Profile file does not exist at
'C:\Users\Admin\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1'.
Chocolatey (choco.exe) is now ready.
You can call choco from anywhere, command line or powershell by typing choco.
Run choco /? for a list of functions.
You may need to shut down and restart powershell and/or consoles
 first prior to using choco.
Ensuring chocolatey commands are on the path
Ensuring chocolatey.nupkg is in the lib folder
```

虽然提示信息中仍然有错误，但研究这个太麻烦了，没有什么意义，具体可查看Chocolatey官网的[安装说明][chocolatey_install]中`More Install Options`项中的详细介绍来解决，但是根据`Chocolatey (choco.exe) is now ready.`可知，Chocolatey已经安装成功，可通过如下命令来验证下，当然在cmd中也可以执行该命令。

```txt
PS C:\WINDOWS\system32> choco -v
0.10.3
```

如果需要更新Chocolatey，只需执行如下命令，至于卸载Chocolatey好像有点麻烦的样子，详见[安装说明][chocolatey_install]，不过一般不会用到的吧。

```powershell
choco upgrade chocolatey
```

[gradle_install]: https://gradle.org/install
[chocolatey_install]: https://chocolatey.org/install