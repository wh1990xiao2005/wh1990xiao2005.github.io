---
title: Android Jetpack之AppCompat - Actionbar篇
date: 2019-03-11 22:05:21
tags: Android
categories: 编程世界
---

今天我们来聊一聊有关AppCompat，作为Android Jetpack系列文章的开篇。说到Android Jetpack，我们先看一下这张图：   

![Jetpack一览](https://imgconvert.csdnimg.cn/aHR0cDovL3VwbG9hZC1pbWFnZXMuamlhbnNodS5pby91cGxvYWRfaW1hZ2VzLzk0MDY5MC0zNTRjODMxNGIzMzZkZjU3LnBuZw)   

从图中我们可以看到，整个Android Jetpack分为了四大部分，而我们今天要讲述的就是Foundation中的AppCompat小节，官方将该部分翻译为“基础”。   
Google官方网站： 
https://developer.android.com/jetpack   
按照Google官方的描述，AppCompat就是指v7 appcompat库。   
> “This library adds support for the Action Bar user interface design pattern. This library includes support for material design user interface implementations.”   

意思是：此库添加了对操作栏用户界面设计模式的支持。这个库包括对Material Design用户界面实现的支持。也就是说，我们可以借助该库，对Material Design有更便捷和兼容性更好的实现。   
进入AppCompat章节后，我们发现它又被分为了4个部分，这4个部分被称为“key class”，也就是重点类，它们分别是：   
 - ActionBar：提供Actionbar用户界面模式的实现；  
 - AppCompatActivity：添加可用作使用支持库操作栏实现的Activity的基类；  
 - AppCompatDialog：添加一个可用作AppCompat主题的Dialog的基类;  
 - ShareActionProvider：添加对可包含在ActionBar中的标准化共享操作（如电子邮件或发布到社交应用程序）的支持。  

想要使用这些类，我们需要添加v7支持库。  
到现在为止，支持库的最新版本是28，添加的库名称和版本如下：  

```
com.android.support:appcompat-v7:28.0.0
```

今天我们就先来聊一聊ActionBar，也是这里面最为复杂的一个部分。  
依稀记得，伴随着Google I/O 2014的召开，早在Android 5.0的时代，Google 官方推出了ToolBar组件，在那之后，ToolBar就登上了历史舞台，扮演着重要的角色。之前我在CSDN上面也发表过相关主题的文章，因为发布的时机刚好是ToolBar登场之际，所以获得了很多的阅读量。快5年过去了，回头再看那几篇连载，感觉文笔很是稚嫩。今天借着讲述Jetpack，再次聊聊ToolBar那些事，相信你我都会有新的收获。  

**首先解决疑问：**
1.  问：既然有了ActionBar，为何还要用ToolBar？ 答：使用AppCompat Toolbar能兼容更广泛的设备（ActionBar要求最低Android 3.0，ToolBar要求最低Android 2.1，但只有Android 5.0及以上才能在不使用AppCompat兼容包的前提下支持Material Design），以及各式各样的自定义需求。

2.  问：ToolBar上面都应该包含哪些内容？ 答：根据Google的指导，应用栏区域应具备以下要素：1）一个专用区域，可以标识您的应用并指示用户在应用中的位置；2）以可预测的方式访问搜索等重要操作；3）支持导航和视图切换（通过标签页或下拉列表）。

**一、添加ToolBar**  
想要添加一个ToolBar，总共3步走：  
1\.  更改application主题样式，操作对象：styles.xml。  
对于新建的Android项目，AndroidManifest.xml中已经定义了所使用的theme，即：  

```
android:theme="@style/AppTheme"
```

此时，我们修改styles.xml文件即可，将默认的继承值改掉，如下所示：  

```
<style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
```

2\. 在Activity布局中添加ToolBar，操作对象：layout布局文件  

```
<android.support.v7.widget.Toolbar
    android:id="@+id/activity_main_tb"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:elevation="4dp"
    android:theme="@style/ThemeOverlay.AppCompat.ActionBar"
    app:popupTheme="@style/ThemeOverlay.AppCompat.Light" />
```

对高度掌握不好火候的同学，直接如上使用ActionBar的高度就可以了。  
android:evevation指“仰角”，这部分知识请参考：
https://developer.android.com/training/material/shadows-clipping  
这里就不再赘述了。
如果你的项目已经迁移到Android X，你的布局文件代码片应该是：  

```
<androidx.appcompat.widget.Toolbar
    android:id="@+id/activity_main_tb"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:elevation="4dp"
    android:theme="@style/ThemeOverlay.AppCompat.ActionBar"
    app:popupTheme="@style/ThemeOverlay.AppCompat.Light" />
```

3\. 在Activity类中找到ToolBar，并应用它，操作对象：Activity类  
这一步并不复杂，参考普通的Android控件。类似地，我们通过findViewById()找到ToolBar，并做执行一些设定，即可完成该步骤，示例如下：  

```
private Toolbar topTb;
topTb = findViewById(R.id.activity_main_tb);        
setSupportActionBar(topTb);
```

一旦我们setSupportActionBar()后，日后我们就可以通过getSupportActionBar()方法来获取ToolBar实例，也可以使用兼容包提供的ActionBar的各种API方法了。  
到此，我们就完成了ToolBar的添加，还算简单吧？  

**二、ToolBar外观的自定义**
不出意外的话，我们运行的结果将会和下图类似：  

![默认的配色方案](https://imgconvert.csdnimg.cn/aHR0cDovL3VwbG9hZC1pbWFnZXMuamlhbnNodS5pby91cGxvYWRfaW1hZ2VzLzk0MDY5MC0zNTI2ZGYxNjMzZDcwMmExLnBuZw)   

大绿底，大黑字，实在不怎么好看。  
那么，如果我们想要自定义配色方案，该如何做呢？参考下图：  

![配色方案指导](https://imgconvert.csdnimg.cn/aHR0cDovL3VwbG9hZC1pbWFnZXMuamlhbnNodS5pby91cGxvYWRfaW1hZ2VzLzk0MDY5MC01NDJjZmEwNzM4MmVjMzgxLmpwZw)  

这些值我们都可以在color.xml中定义，并在styles.xml中引用。下图是一个重新定义配色方案后的截图：  

![自定义的配色方案](https://imgconvert.csdnimg.cn/aHR0cDovL3VwbG9hZC1pbWFnZXMuamlhbnNodS5pby91cGxvYWRfaW1hZ2VzLzk0MDY5MC1jYjMxN2Y4YzA1ZGE5ZWY4LnBuZw)  

相关的代码片：  

**color.xml**  

```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="colorPrimary">#002FA7</color>
    <color name="colorPrimaryDark">#001F67</color>
    <color name="colorAccent">#003FB7</color>
    <color name="textColorPrimary">#FFFFFF</color>
</resources>
```

**styles.xml**  

```
<style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
    <item name="colorPrimary">@color/colorPrimary</item>
    <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
    <item name="colorAccent">@color/colorAccent</item>
    <item name="android:textColorPrimary">@color/textColorPrimary</item>
</style>
```

细心的读者会发现，后面的截图中，右上角多了菜单项，这又是如何实现的呢？我们继续往后看。  

**三、给ToolBar增加动作**  
首先我们来看看如何给ToolBar增加菜单，我们依然分为3步完成。   
1\.  编写菜单xml文件，操作对象：menu文件夹下的菜单xml文件  
这里我添加了两个菜单，如上图所示，一个隐藏在“更多”里，另一个是搜索。如下代码片所示：  

```
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <item
        android:id="@+id/menu_main_info"
        android:title="@string/menu_main_activity_info"
        app:showAsAction="never" />

    <item
        android:id="@+id/menu_main_search"
        android:title="@string/menu_main_activity_search"
        app:actionViewClass="android.support.v7.widget.SearchView"
        app:showAsAction="always" />
</menu>
```

如上，我们可以看到有两个item，分别对应Info和搜索，我们使用"app:showAsAction"的值来控制这个菜单是否显示，常见的值有always，ifRoom，never。从字面上也很好理解，这里就不多解释了。   

2\. 接下来是Java代码片段：   

```
private SearchView tbSearchSv;

@Override
public boolean onCreateOptionsMenu(Menu menu) {
    getMenuInflater().inflate(R.menu.main_activity_menu, menu);
    MenuItem searchItem = menu.findItem(R.id.menu_main_search);
    tbSearchSv = (SearchView) searchItem.getActionView();
    return super.onCreateOptionsMenu(menu);
}
```

细心的朋友会发现有这一行：   

```
app:actionViewClass="android.support.v7.widget.SearchView"
```   

它是做什么的呢？  

![搜索栏效果](https://imgconvert.csdnimg.cn/aHR0cDovL3VwbG9hZC1pbWFnZXMuamlhbnNodS5pby91cGxvYWRfaW1hZ2VzLzk0MDY5MC1kNjNiMDRjYjcyYWNiNDBmLnBuZw)   

对了！它就是搜索栏，是原生的搜索栏。所以某些情况下，这个搜索栏是不用自己去实现的，系统已经给我们提供了SearchView！  
典型的APP：网易云音乐、知乎上方的搜索都是这样的。   

3\. 为菜单设置监听器，我们先来看最普通的Info按钮，我们只需在Java代码中Override指定的方法就可以了，如下所示：   

```
@Override
public boolean onOptionsItemSelected(MenuItem item) {
    switch (item.getItemId()) {
        case R.id.menu_main_info:
            Toast.makeText(MainActivity.this, R.string.menu_main_activity_info, Toast.LENGTH_LONG).show();
            break;
    }
    return super.onOptionsItemSelected(item);
}
```

对于搜索栏，首先我们想到的是，如何获取用户输入的内容呢？  
其实很简单，玄机在于SearchView，只需对SearchView添加监听器就可以了。  

```
tbSearchSv.setOnQueryTextListener(new SearchView.OnQueryTextListener() {
    @Override
    public boolean onQueryTextSubmit(String s) {
        
        return false;
    }

    @Override
    public boolean onQueryTextChange(String s) {
        
        return false;
    }
});
```

这里要注意，设置监听器前，要确保SearchView（这里的tbSearchSv）已经被实例化，否则，会出现空指针异常崩溃。   
关于SearchView，还有一写额外的设置，比如：   

```
// 设置提交按钮是否可见（默认不可见）
tbSearchSv.setSubmitButtonEnabled(true);
```

![提交按钮是否可见](https://imgconvert.csdnimg.cn/aHR0cDovL3VwbG9hZC1pbWFnZXMuamlhbnNodS5pby91cGxvYWRfaW1hZ2VzLzk0MDY5MC1jYWQyNDA4MThlZGZkOTMwLnBuZw)   

```
// 设置左侧是否显示搜索图标（默认不可见）
tbSearchSv.setIconifiedByDefault(false);
```

![左侧是否显示搜索图标](https://imgconvert.csdnimg.cn/aHR0cDovL3VwbG9hZC1pbWFnZXMuamlhbnNodS5pby91cGxvYWRfaW1hZ2VzLzk0MDY5MC0yMTAzODEyMzQzMWE5OGU4LnBuZw)    
更多可使用的API请参考官方文档：
https://developer.android.google.cn/reference/android/widget/SearchView  

不过，我们这里还需要做最后一点善后。如果你是一路下来照着本篇文章敲代码的话，在搜索框打开的情况下按一下返回键，你期待的是什么？是不是取消搜索操作，停留在当前界面？然而实际上是……退出了APP。   
所以我们这里要对返回键的默认动作做一个“拦截”，具体可参考如下代码片：   

```
@Override
public boolean onKeyUp(int keyCode, KeyEvent event) {
    switch (keyCode) {
        case KeyEvent.KEYCODE_BACK:
            if (!tbSearchSv.isIconified()) {
                tbSearchSv.setIconified(true);
                return true;
            }
            break;
    }
    return super.onKeyUp(keyCode, event);
}
```

这里SearchView的isIconfied()方法可以返回当前的SearchView展开状态。  

**四、返回上一层**   

ToolBar还有一个比较常见的功能就是左上角的返回按钮，提供返回上一层操作，很多的APP开发者都习惯于自定义一个ImageButton或类似的空间，然后使用美工提供的图像素材，设置监听器，写Selector……一套下来，费时费力。  
其实Google已经为开发者提供了现成的非常易用的返回逻辑处理。要实现这些处理，两步就搞定了。  

1\. 在ToolBar对象上启用返回钮  

```
getSupportActionBar().setDisplayHomeAsUpEnabled(true);
```

这里注意，虽然之前将ToolBar通过setSupportActionBar()方式当做参数被set了一次，但是ToolBar类本身并不提供setDisplayHomeAsUpEnabled()方法，因此，我们还需要getSupportActionBar()，先获取ActionBar对象，然后使用该对象，而不是直接使用ToolBar对象。  

2\. 在AndroidManifest.xml中定义要跳转的Activity  
如题，我们在AndroidManifest.xml中，对子Activity做处理，这里不要忘记兼容低版本的系统。  

```
<activity
    android:name=".MainActivity"
    android:parentActivityName=".SecondActivity">
    <!-- 兼容 Android4.0 及以下版本-->
    <meta-data
        android:name="android.support.PARENT_ACTIVITY"
        android:value=".SecondActivity" />
</activity>
```

将SecondActivity改为入口Activity，然后重新运行程序，将实现如下效果：  

![返回键视频演示](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy85NDA2OTAtMWJmZDM2NDg0NTczMzg1Zi5naWY)   


到此，关于ToolBar常见用法的梳理告一段落。源码请自取：
https://github.com/wh1990xiao2005/JetpackDemo    

我会在接下来的文章中，和大家分享关于ToolBar的剩余内容，以及AppCompat兼容包中的其他知识，希望对你我都有帮助。   
共勉！