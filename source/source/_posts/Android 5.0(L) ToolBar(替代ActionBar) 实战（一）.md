---
title: Android 5.0(L) ToolBar(替代ActionBar) 实战（一）
date: 2014-10-30 19:32:59
tags: Android
categories: 编程世界
---

Android 5.0 SDK 在上周悄然发布了，由于更新了Appcombat V7库，将Google在IO 2014时提到的Material Design的UI界面元素带了进来。从此，开发者可以创建基于Material Design界面设计的APP了。   
诚然，在更新了新版SDK并引入新的Appcombat库后，发现之前的ActionBar的很多方法已经标记为不建议（Deprecated）的了。经过反复摸索，得知需要用ToolBar来代替古老的ActionBar。   
那么ToolBar与传统ActionBar相比，有何优越性呢？简单来说：多样、自由。下面来一起看看ToolBar的使用。   
首先，我们来新建一个项目   

![新建项目](https://img-blog.csdn.net/20141030193650197?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHdobmV3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)  

为了更好的兼容之前的版本，因此依旧将最低兼容的Android版本设置为2.2。其他保持默认，一路下一步，然后完成。   
为了方便开发者，在新建项目的时候Eclipse依然是自动引入了Appcombar库。如果这个时候代码报错，则要看看这个库的引入状况，并且检查一下是否作为Library被导入到新的项目中了。   
一切就绪的话，进入AndroidMenifest.xml，将ActionBar彻底废掉。废掉老旧的ActionBar的方法十分简单，将application下android:theme元素替换为：@style/Theme.AppCompat.NoActionBar就可以了。当然，下面的activity中切记不要再使用带有ActionBar的主题样式了，否则会报错的。另外，android:label对于Toolbar而言，也没有必要存在了，当然，即使存在这个字段是不会报错的。下面放上整个xml的例子：    

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.xwh.toolbardemo"
    android:versionCode="1"
    android:versionName="1.0" >
 
    <uses-sdk
        android:minSdkVersion="8"
        android:targetSdkVersion="21" />
 
    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/Theme.AppCompat.NoActionBar" >
        <activity
            android:name=".MainActivity"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
 
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
 
</manifest>
```

下面我们来到MainActivity的布局文件：activity_main.xml。如果你的SDK已经更新为最新而且此项目也是配置为5.0的编译状态下时，在布局预览视图中将会如下所示：    

![布局预览 深色主题](https://img-blog.csdn.net/20141030193706094?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHdobmV3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

可以看到，预览窗格中，已经完全是5.0的UI设计风格了。至于为什么是深色的主题，是因为之前在AndroidMenifest.xml中设置了Theme.AppCompat.NoActionBar。换言之，如果我们设置成Theme.AppCompat.Light.NoActionBar，则会呈现浅色的主题。   

![布局预览 浅色主题](https://img-blog.csdn.net/20141030193718168?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHdobmV3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

下面切换到代码视图。对于ActionBar，我们无需关心在xml布局中的位置，只需要在Java代码中进行设置即可。而对于ToolBar，是要求我们自己写到Layout（布局xml）中的。这也就意味着它可以出现在任意一个我们希望它出现的位置，甚至能够同时存在多个ToolBar，并且互不干扰（参考多个ImageView，或者多个TextView等等）。   
我们可以通过创建android.support.v7.widget.Toolbar控件来显示自定义的Toolbar，参考下面的代码：   

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:app="http://schemas.android.com/apk/res/com.xwh.toolbardemo"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.xwh.toolbardemo.MainActivity" >
 
    <android.support.v7.widget.Toolbar
        android:id="@+id/demo_toolbar"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:background="?attr/colorPrimary"
        android:minHeight="?attr/actionBarSize"
        app:title="@string/hello_world" />
 
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@+id/demo_toolbar"
        android:text="@string/hello_world" />
 
</RelativeLayout>
```

关于Toolbar的可用属性，这里不多做赘述，大家可以去安卓开发者官网自行搜索API。   
到此，这个Toolbar已经可以正常显示了，并且其标题为“Hello World”。而且诸位可以发现，从Toolbar的创建到显示，我们还没有写过一句Java代码。   
下面放上运行效果截图：   

![运行效果截图](https://img-blog.csdn.net/20141030193730102?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHdobmV3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)  
在下一篇博文中，我们将集中介绍如何使用Toolbar，结合ViewPager，实现类似微信的滑动切换多视图效果。  