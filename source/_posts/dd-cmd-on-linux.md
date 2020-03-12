---
title: Linux下DD命令进度查看
date: 2020-03-11 02:25:06
tags: [Linux,cmd]
categories: Note
---

在Ubuntu 系统上执行如下命令

```shell
 dd if=/img.iso of=/dev/mydevice bs=10M count=100000
```
<!--more-->
有的时候，可能会非常耗时，这个时候，如果让dd命令输出执行进度信息，会非常有用。

重新打开一个Shell，然后执行如下命令即可每秒输出一次进度信息

```Shell
 watch -n 1 pkill -USR1 -x dd
```