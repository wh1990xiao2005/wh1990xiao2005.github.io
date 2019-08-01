---
title: 解决TextView引起的莫名崩溃
date: 2017-04-06 09:50:35
tags: Android
categories: 编程世界
---
进了2017年之后到现在，我一直都在忙着做一款IM应用，功能和微信、QQ类似。  
在开发和测试的过程中，发现了几个可以总结记录的东西，今天和大家来分享其中之一。    
首先来看一个异常：    

> 
    java.lang.ArrayIndexOutOfBoundsException: length=125; index=-1
	at android.text.StaticLayout.calculateEllipsis(StaticLayout.java:830)
	at android.text.StaticLayout.out(StaticLayout.java:749)
	at android.text.StaticLayout.generate(StaticLayout.java:453)
	at android.text.StaticLayout.<init>(StaticLayout.java:145)
	at android.widget.TextView.makeSingleLayout(TextView.java:6298)
	at android.widget.TextView.makeNewLayout(TextView.java:6144)
	
乍看上去，倒是和我这边没什么关系，都是Android内部实现的问题。但是每到这个地方，都会崩溃，100%复现。这对于实际用户使用而言不是什么好事。  
于是Google，百度了一圈，发现了Android系统在某个版本中的“坑”。  
具体解决办法：  
之前xml布局中，对于TextView：  

```
<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:ellipsize="middle"
    android:lines="1"
    android:text=""/>  
```

看上去没有问题，如果要解决上述崩溃问题，改为：

```
<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:ellipsize="middle"
    android:singleLine="true"
    android:text=""/>  
```

即可。  
这里参考了：[Google Code][1]


  [1]: https://code.google.com/p/android/issues/detail?id=33868
  
上述问题在Android 4.4版本上有所体现，其他的版本暂不确定。