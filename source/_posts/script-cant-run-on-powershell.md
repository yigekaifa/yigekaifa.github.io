---
title: PowerShell禁止执行脚本的解决方法
date: 2020-03-11 02:26:23
tags: [PowerShell,Script]
categories: Note
---

如果出现类似下面的提示：

```bash
无法加载文件 ******.xxx，因为在此系统中禁止执行脚本。有关详细信息，请参阅 "get-help about_signing"。
所在位置 行:1 字符: 17

```

解决方法：
<!--more-->
运行`Get-ExecutionPolicy`。如果返回`Restricted`，然后运行`set-ExecutionPolicy RemoteSigned`

然后就可以运行命令了。

另外如果还不能执行，例如你npm安装的一些命令不能运行，那么请你把`C:\Users\用户名\AppData\Roaming\npm`添加到系统变量`Path`里面。