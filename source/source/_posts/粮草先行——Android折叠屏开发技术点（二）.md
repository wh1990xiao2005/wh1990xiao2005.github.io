---
title: 粮草先行——Android折叠屏开发技术点（二）
date: 2019-02-20 17:56:10
tags: Android
categories: 编程世界
---

继该系列的第一篇和番外篇之后，今天我们来聊一聊多窗口开发的注意事项。实际上，与其说“多窗口开发”，不如说让我们的APP**适应多窗口模式**。  
可能有朋友会问，为什么要提到多窗口模式呢？  
这是因为：

1. 折叠屏在展开后的屏幕会变大，而变大带来的变化就是多窗口运行；  
2. 多窗口模式在很早之前就已经被Google官方支持，提供了相应的API，现在也到了该了解一下的时候了；
3. 避免我们辛辛苦苦开发好的APP，翻车在多窗口的沟里；
4. 虽然在Android Q中，多窗口模式很可能将成为默认行为，但要兼容之前的版本，我们仍然需要做一些事情。  

首先我们来看一下如果我们什么都不做，切换多窗口时，会发生什么呢？仍然从生命周期的角度来解读：  

*失去焦点（未做兼容处理）*

>D/MainActivity: onPause

*重新获得焦点（未做兼容处理）*

>D/MainActivity: onResume

注意，在默认情况下，一旦失去焦点，会回调onPause()方法。而此时Activity仍然可以被用户看到，因此，如果我们在onPause()里面做了一些不合适的操作，比如来了一个手势解锁，或者特殊情况下直接关闭程序，就明显不合适了。  
为了避免这种情况出现，我们希望在失去焦点的时候不回调onPause()。那么，我们只需在AndroidManifest.xml的application节点下添加如下代码，即可规避该问题：
```
<meta-data
  android:name="android.allow_multiple_resumed_activities"
  android:value="true"/>
```
再次测试时，我们发现onPause()已经不会被回调了。  
到这里，我们有这样一个疑问：我们失去onPause()作为得到/失去焦点的判定依据，我们用什么来得知状态呢？  
很简单——**借助onWindowFocusChanged()回调**，即可及时获取焦点状态了。使用如下代码片进行测试：  
```
@Override
public void onWindowFocusChanged(boolean hasFocus) {
    super.onWindowFocusChanged(hasFocus);
    Log.d(TAG, "onWindowFocusChanged - " + hasFocus);
}
```
使APP反复得到/失去焦点，观察Logcat输出，得到如下结果：
>D/MainActivity: onWindowFocusChanged - false  
D/MainActivity: onWindowFocusChanged - true  
D/MainActivity: onWindowFocusChanged - false  
D/MainActivity: onWindowFocusChanged - true  
……

至此，问题解决。  
然而，你可能还会问：**如果小窗口的尺寸发生变化如何处理？**  
这并不困难，通过调整APP窗口大小，再次观察Logcat输出，发现**onConfigurationChanged()方法被回调**了。还记得我们之前提过的改变窗口大小吗？这就是一个实际的例子。该如何处理，大家心中应该有数了吧。  
今天的分享就到这里，希望上面的内容能够对你有帮助。
