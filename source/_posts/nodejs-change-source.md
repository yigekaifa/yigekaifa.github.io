---
title: npm切换淘宝源
date: 2020-03-12 12:51:05
tags: [nodejs,npm,cnpm,源地址,淘宝源]
categories: Note
---

全局配置切换到淘宝源
```bash
npm config set registry https://registry.npm.taobao.org
```
<!--more-->

临时使用淘宝源
```bash
npm --registry https://registry.npm.taobao.org install node-red-contrib-composer@latest
```

全局配置切换到官方源
```bash
npm config set registry http://www.npmjs.org
```
检测是否切换到了淘宝源
```bash
npm info underscore
```

另外：
> 默认的npm下载地址：http://www.npmjs.org/
> 淘宝npm镜像的地址：https://npm.taobao.org/
> 你也可以使用CNPM：https://cnpmjs.org