---
title: 监听布局内所有的EditText
date: 2020-03-10 23:49:23
tags: [Edittext,ViewGroup,CustomView]
categories: Dev
---

当出现布局内有N个输入框且需要同步监听，例如你需要监听用户输入所有内容之后才可以点击下一步的控件，你可以试试下面这个控件。


<!--more-->

## 1. 获取子view

编历布局，看当前布局有几个`EditText`,然后去监听拿到`focus`的`view`

获取子`view`的方式：

```java
// 遍历viewGroup
    public int traverseViewGroup(View view) {
        int viewCount = 0;
        if (null == view) {
            return 0;
        }
        if (view instanceof ViewGroup) {
            //遍历ViewGroup,是子view加1，是ViewGroup递归调用
            for (int i = 0; i < ((ViewGroup) view).getChildCount(); i++) {
                View child = ((ViewGroup) view).getChildAt(i);
                if (child instanceof ViewGroup) {
                    viewCount += traverseViewGroup(((ViewGroup) view).getChildAt(i));
                } else {
                    viewCount++;
                }
            }
        } else {
            viewCount++;
        }
        return viewCount;
    }
```

非递归方法获取子`view`：
```java
 // 遍历viewGroup
    public int traverseViewGroup(View view) {
        int viewCount = 0;
        if (null == view) {
            return 0;
        }
        if (view instanceof ViewGroup) {
            ViewGroup viewGroup = (ViewGroup) view;
            LinkedList<ViewGroup> linkedList = new LinkedList<>();
            linkedList.add(viewGroup);
            while (!linkedList.isEmpty()) {
                //removeFirst()删除第一个元素，并返回该元素
                ViewGroup current = linkedList.removeFirst();
                viewCount++;
                //遍历linkedList中第一个viewGroup中的子view
                for (int i = 0; i < current.getChildCount(); i++) {
                    if (current.getChildAt(i) instanceof ViewGroup) {
                        linkedList.addLast((ViewGroup) current.getChildAt(i));
                    } else {
                        viewCount++;
                    }
                }
            }
        } else {
            viewCount++;
        }
        return viewCount;
    }
```

获取到子`view`的时候，你需要判断这个子`view`是不是`EditText`，方法有两种：

```java
v.getClassName() == "EditText"
```

```java
v instanceof Button
```

## 2. 拿到子view之后：

方法1. 拿到子`view`之后，不需要每个都去监听（当然你也可以这么做），另外一种，判断在`viewgroup`中哪个子`view`获取到了焦点，然后给他添加监听，其他的移除监听。

然后给设置的按钮去设置`enable`

方法2. LiveData监听数据变化，但是和上面的方式换汤不换药，代码量更多了一点。LiveData的具体使用可以参考： https://developer.android.google.cn/reference/android/arch/lifecycle/LiveData


### 需要完成的内容：

- [x] 添加例外的参数，例如非必填的Edittext