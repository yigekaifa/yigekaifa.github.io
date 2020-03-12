---
title: startWithPop()出现两个Fragment都退出的情况
date: 2020-03-10 23:37:18
tags: [startWithPop,Fragment]
categories: Dev
---

有时候`startWithPop()`会出现两个`Fragment`都出栈的情况，一般当前`fragment`是RootView的时候会出现这个情况。

<!--more-->

解决方法：

```java
((BaseFragment) getParentFragment()).start(toFragment);
```
或者
```java
popTo(A.class,true,new Runnable(){
       @Override
       public void run() {
            start(B);
       }
},getFragmentAnimator().getPopExit()); // getFragmentAnimator().getPopExit() 代表popTo时的动画
```
