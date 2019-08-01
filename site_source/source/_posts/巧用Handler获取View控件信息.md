---
title: 巧用Handler获取View控件信息
date: 2019-01-14 23:12:09
tags: Android
categories: 编程世界
---

众所周知，在Android实际开发中，对于某些复杂多变的情况，控件的位置摆放、大小控制并非是xml类型的layout文件完全可以搞定的。此时，我们通常会使用Java代码来通过动态计算，将指定的控件摆放在相应的位置，并限定其大小。同样地，也需要获取某个控件的大小。
对于获取控件宽、高的方法，大家可以自行谷歌或者百度，大抵无非一下三种方法：

 1. 给相应的View控件**添加ViewTreeObserver回调**；
 2. **Override onWindowFocusChange方法**；
 3. 在需要测量时（而不是onCreate或onResume中），**使用MeasureSpec内部类**获取宽高。

对于上述第三种情况，我们暂且不论。对于前二者而言，有没有更简单的实现呢？

## 为何获取宽高要如此？
对于初学者，可能会有这样的疑问：为什么我们不能在onCreate()或者onResume()中直接使用上述第三种方案获取宽高呢？
结论是：那样的话，获取来的值很可能皆为0，即使实际的宽高不是0。那么这是为何呢？
这其实是由Android的UI绘制流程决定的。大家不妨试着做一下实验，即使是在onResume()方法后，它的意义也仅仅是指Activity进入了可见的**状态**，这**并不意味着界面绘制的结束**。我们可以用一个简单的带有宽高值得View来做实验，观察Activity中各回调方法的调用顺序，得到的结果将是这样的：

> Activity.oncreate() → Activity.onResume() → View.onMeasure() → View.onLayout() → onGlobalLayoutListener() → Activity.onWidnowFocusChanged() → ... → View.onDraw() -> ...

因此，如果我们在onResume()中尝试获取View宽高的话，很大概率是会失败的。

## 巧用Handler获取View控件信息
这里我们开门见山地先放上代码片：

```
private int[] measureView(final View view) {
    final int[] returnData = new int[2];
    view.post(new Runnable() {
        @Override
        public void run() {
            returnData[0] = view.getWidth();
            returnData[1] = view.getHeight();
            Log.i(TAG, "Width: " + returnData[0] + ", height: " + returnData[1]);
        }
    });
    return returnData;
}
```
上述代码作为通用的方法将获取任意View的宽高做了封装，其妙处就在‘view.post’处。
将其置于onCreate()、onResume()方法中调用，均可获取到正确的宽高。

```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    Log.i(TAG, "onCreate start!");
    setContentView(R.layout.activity_main);
    testTv = (TextView) findViewById(R.id.testTv);
    measureView(testTv);
}

@Override
protected void onResume() {
    super.onResume();
    Log.i(TAG, "onResume start!");
    measureView(testTv);
}
```

Logcat中的运行结果：

> 2019-01-14 22:33:13.874 18355-18355/com.example.wenhan.helloandroid I/MainActivity: Width: 225, height: 57
2019-01-14 22:33:13.874 18355-18355/com.example.wenhan.helloandroid I/MainActivity: Width: 225, height: 57

## 为何如此就可获取到正确的值了呢？
其中的玄机在于，我们在View.post()中所写的语句并没有立即执行，而在其真正执行的时候，View的宽高已经被测量完成了，那时我们再去获取宽高时，就会很容易地获取到正确的值了。
通过断点Debug，可以轻松地发现，在Activity启动过程的调用栈中，存在ActivityThread类被执行了，具体按照：

> main() -> handleResumeActivity() -> addView() -> setView() -> requestLayout() -> scheduleTraversals() -> 执行mTraversalRunnable异步线程 -> doTraversal() -> performTraversals() -> ... -> performMeasure() ->  ...

的执行顺序。
在我们获取宽高的语句执行前，主线程的Handler正在执行TraversalRunnable（见上述方法具体实现），而performMeasure也被包含其中。又因为我们获取宽高的语句要排队，处于等待状态，直到主线程Handler轮到执行我们的语句，而此时View的宽高的测量已经结束。

完整示例代码：https://github.com/wh1990xiao2005/FetchViewSizeDemo
