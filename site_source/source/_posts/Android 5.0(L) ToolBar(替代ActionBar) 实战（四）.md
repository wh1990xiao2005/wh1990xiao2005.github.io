---
title: Android 5.0(L) ToolBar(替代ActionBar) 实战（四）
date: 2014-11-05 16:30:22
tags: Android
categories: 编程世界
---

休息了三天后，我又回来了，我们继续讨论Toolbar。   
如果此前你是跟着连载一步一步走下来的，那么你应该会注意到MainActivity是一直在报警告的。因为其中我们声明了toolbar对象，但是一直没有使用。如果要实现菜单功能的话，就要使用了。   
结合Toolbar使用菜单还是很容易的。只需在MainActivity中重写onCreateOptionsMenu()和onOptionsItemSelected()就可以了。这种方法已经有着悠久的历史，这里就不再多说了。   
回忆一下ActionBar，此前的ActionBar在实现菜单时，如果遇到虚拟按键的机器，会在Activity的右上角显示菜单。如果遇到实体按键的机器，则不会，用户需要手动去触摸菜单键才能激活菜单。这其实是不太合理的，作为用户，很有可能不知道应用中还有菜单。因此，在Toolbar中，这个问题得到了很好的修复。效果就是，无论是否存在实体按键，右上角都会显示菜单，而要实现这一效果也是及其容易的，只需要下面的一句话：   

```
setSupportActionBar(toolbar);
```

下面放上效果图：   

![显示菜单项](https://img-blog.csdn.net/20141105161803835?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHdobmV3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

细心的朋友可以发现，上方的Toolbar已经改变了样式，那么它又是如何实现的呢？   
这里有两种方法：   

 - 在每个具有Toolbar的布局文件中定义风格；
 - 在AndroidManifest中指向统一的应用主题样式。

前者不必多说，只需要在布局文件中指定特定字段的值就好。这里详细说下后者。   
首先我们在布局文件中，除了Toolbar的位置、标题文本和ID外，不要给定任何其他的值，比如：   

```
 <android.support.v7.widget.Toolbar
        android:id="@+id/demo_toolbar"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        app:title="@string/hello_world" />
```

然后，在values文件夹中创建theme.xml，在其中定义主题风格，下面列出这段程序清单：   

```
<?xml version="1.0" encoding="utf-8"?>
<resources xmlns:android="http://schemas.android.com/apk/res/android">
 
    <!-- 默认的蓝色风格 -->
    <style name="DefaultBlueTheme" parent="@style/Theme.AppCompat.Light.NoActionBar">
        <!-- Toolbar风格 -->
        <item name="toolbarStyle">@style/DefaultBlueToolbar</item>
    </style>
 
    <!-- 默认的Toolbar样式 -->
    <style name="DefaultBlueToolbar" parent="@style/Widget.AppCompat.Toolbar">
        <item name="android:background">@color/default_blue</item>
        <item name="titleTextAppearance">@style/ToolBarTitleTextStyle</item>
    </style>
 
</resources>
```

引用的颜色值如下，在color.xml中定义：   

```
<color name="default_blue">#33B5E5</color>
<color name="default_white">#FFFFFF</color>
```

这样就实现了上图的效果。