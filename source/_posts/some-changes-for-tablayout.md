---
title: TabLayout的简单修改
date: 2020-03-10 23:11:19
tags: [TabLayout,widget,custom]
categories: Dev
---

设计稿上的tab布局我用了design库的tablayout，根据设计上的图来看，选中的tab要是粗体，下方横线等宽于字体。
<!--more-->

### 问题1：TabLayout无法控制当前选中的行的字体

解决方案：`setCustomView()` 。

首先写一个方法，用来返回每个tab的view：

```java
    public View getTabView(int position, String title) {
        View view = LayoutInflater.from(getContext()).inflate(R.layout.tab_item, null);
        TextView txt_title = (TextView) view.findViewById(R.id.textView);
        txt_title.setText(title);
        return view;
    }
```

然后把view设置到tablayout中：
```java
tabLayout.getTabAt(0).setCustomView(getTabView(0, "全部"));
```

然后去选择控制tablayout的标签的样式，我是新建了一个OnTabSelectedListener，然后再里面进行设置的

```java
OnTabSelectedListener onTabSelectedListener = new TabLayout.OnTabSelectedListener() {
            @Override
            public void onTabSelected(TabLayout.Tab tab) {
                // 选中的tab设置为粗体
                TextView textView = tabLayout.getTabAt(tab.getPosition()).getCustomView().findViewById(R.id.textView);
                textView.setTypeface(null, Typeface.BOLD);
                viewPager.setCurrentItem(tab.getPosition());
            }

            @Override
            public void onTabUnselected(TabLayout.Tab tab) {
                // 未选中的tab设置为正常
                TextView textView = tabLayout.getTabAt(tab.getPosition()).getCustomView().findViewById(R.id.textView);
                textView.setTypeface(null, Typeface.NORMAL);

            }

            @Override
            public void onTabReselected(TabLayout.Tab tab) {

            }
        };
```

但是，设置好之后出现了新的问题：
### 问题2：下面`Indicator`宽度始终为最宽的宽度

即使你设置了`app:tabIndicatorFullWidth="true"`，因为`tabIndicatorFullWidth`是根据默认布局的文字宽度来设置的，我们自定义布局之后，系统无法获取到文字大小和长度，所以就会设置成最宽的宽度。

我的解决方法：

```java
public void reflex(final TabLayout tabLayout) {
        tabLayout.post(new Runnable() {
            @Override
            public void run() {
                try {
                    LinearLayout mTabStrip = (LinearLayout) tabLayout.getChildAt(0);
                    for (int i = 0; i < mTabStrip.getChildCount(); i++) {
                        View tabView = mTabStrip.getChildAt(i);

                        Field mTextViewField = tabView.getClass().getDeclaredField("textView");
                        mTextViewField.setAccessible(true);

                        TextView mTextView = (TextView) mTextViewField.get(tabView);

                        tabView.setPadding(0, 0, 0, 0);

                        int width = 0;
                        width = mTextView.getWidth();
                        if (width == 0) {
                            mTextView.measure(0, 0);
                            width = mTextView.getMeasuredWidth();
                        }

                        LinearLayout.LayoutParams params = (LinearLayout.LayoutParams) tabView.getLayoutParams();
                        params.width = width;

                        WindowManager wm1 = ((Activity) getContext()).getWindowManager();
                        int width1 = wm1.getDefaultDisplay().getWidth();
                        LogUtils.d("" + width1 / tabLayout.getTabCount() + "=============" + mTextView.getText().length() * dip2px(tabLayout.getContext(), 15));

                        params.leftMargin = (width1 / tabLayout.getTabCount() - mTextView.getText().length() * dip2px(tabLayout.getContext(), 15)) / 2;
                        params.rightMargin = (width1 / tabLayout.getTabCount() - mTextView.getText().length() * dip2px(tabLayout.getContext(), 15)) / 2;
                        tabView.setLayoutParams(params);

                        tabView.invalidate();
                    }

                } catch (NoSuchFieldException e) {
                    e.printStackTrace();
                } catch (IllegalAccessException e) {
                    e.printStackTrace();
                }
            }
        });

    }

    public static int dip2px(Context context, float dpValue) {
        float scale = context.getResources().getDisplayMetrics().density;
        return (int) (dpValue * scale + 0.5f);
    }
```

`reflex(tabLayout);`调用在所有`getTabAt`方法之后即可解决这个，但是还有点小问题，tab大小被设置成了文字宽度…这个问题暂时保留，下次解决。另外有几个小问题

1. 去掉tab的水波纹效果，无论是support还是AdnroidX，都用`app:tabRippleColor="@android:color/transparent"`，`app:tabBackground="@android:color/transparent"`即使在support下也无效