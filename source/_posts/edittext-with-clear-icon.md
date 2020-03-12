---
title: 自带清除内容功能的EditText
date: 2020-03-11 02:23:58
tags: [Edittext,Custom]
categories: Dev
---

出发需求：

1. 减少手写清除EditText内容的代码
2. xml里面写的话，点击清除按钮会失去焦点，然后软键盘消失，需要重复点击输入框才可以继续输入

<!--more-->

```java

public class ClearEditText extends AppCompatEditText implements View.OnFocusChangeListener, View.OnTouchListener, TextWatcher {

    private Drawable mClearTextIcon;
    private OnFocusChangeListener mOnFocusChangeListener;
    private OnTouchListener mOnTouchListener;
    private boolean isShowClearIcon;

    public ClearEditText(Context context) {
        super(context);
        init(context, null);
    }

    public ClearEditText(Context context, AttributeSet attrs) {
        super(context, attrs);
        init(context, attrs);
    }

    public ClearEditText(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        init(context, attrs);
    }

    private void init(Context context, AttributeSet attrs) {
        mClearTextIcon = getCompoundDrawables()[2];
        TypedArray typedArray = context.obtainStyledAttributes(attrs, R.styleable.ClearEditText);
        isShowClearIcon = typedArray.getBoolean(R.styleable.ClearEditText_isShowClearIcon, true);
        if (mClearTextIcon == null) {
            final Drawable drawable = ContextCompat.getDrawable(context, R.mipmap.icon_clear_text);
            final Drawable wrapDrawable = DrawableCompat.wrap(drawable);
            DrawableCompat.setTint(wrapDrawable, getCurrentHintTextColor());
            mClearTextIcon = wrapDrawable;
        }

        mClearTextIcon.setBounds(0, 0, mClearTextIcon.getIntrinsicHeight(), mClearTextIcon.getIntrinsicHeight());
        setClearIconVisible(false);
        super.setOnFocusChangeListener(this);
        super.setOnTouchListener(this);
        addTextChangedListener(this);
    }

    public void setmOnFocusChangeListener(OnFocusChangeListener mOnFocusChangeListener) {
        this.mOnFocusChangeListener = mOnFocusChangeListener;
    }

    public void setmOnTouchListener(OnTouchListener mOnTouchListener) {
        this.mOnTouchListener = mOnTouchListener;
    }

    private void setClearIconVisible(boolean visible) {
        mClearTextIcon.setVisible(visible && isShowClearIcon, false);
        final Drawable[] compoundDrawables = getCompoundDrawables();
        setCompoundDrawables(
                compoundDrawables[0],
                compoundDrawables[1],
                visible ? mClearTextIcon : null,
                compoundDrawables[3]);
    }

    @Override
    public void onFocusChange(View v, boolean hasFocus) {
        if (hasFocus) {
            setClearIconVisible(getText().length() > 0);
        } else {
            setClearIconVisible(false);
        }
        if (mOnFocusChangeListener != null) {
            mOnFocusChangeListener.onFocusChange(v, hasFocus);
        }
    }

    @Override
    public boolean onTouch(View v, MotionEvent motionEvent) {
        int x = (int) motionEvent.getX();
        if (mClearTextIcon.isVisible() && x > getWidth() - getPaddingRight() - mClearTextIcon.getIntrinsicWidth()) {
            if (motionEvent.getAction() == motionEvent.ACTION_UP) {
                setError(null);
                setText("");
            }
            return true;
        }
        return (mOnTouchListener != null && mOnTouchListener.onTouch(v, motionEvent));

    }

    public void isShowClearIcon(boolean isShowClearIcon) {
        this.isShowClearIcon = isShowClearIcon;
        setClearIconVisible(getText().length() > 0);
    }

    @Override
    public void onTextChanged(CharSequence text, int start, int lengthBefore, int lengthAfter) {
        if (isFocused()) {
            setClearIconVisible(text.length() > 0);
        }

    }

    @Override
    public void beforeTextChanged(CharSequence s, int start, int count, int after) {

    }

    @Override
    public void afterTextChanged(Editable s) {

    }
}

```

attrs：

```xml
    <declare-styleable name="ClearEditText">
        <!--是否需要展示清除按钮-->
        <attr name="isShowClearIcon" format="boolean"/>
    </declare-styleable>
```

使用
```xml
<xxx.ClearEditText>
    <!--some parms here-->
    ...
    
    <!--set is show the icon here-->
    app:isShowClearIcon = "true"
</ClearEditText>
```

或者在`java`代码中:

```java
clearTextView.isShowClearIcon(true);
```