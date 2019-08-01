---
title: 粮草先行——Android折叠屏开发技术点（一）
date: 2019-01-30 14:58:37
tags: Android
categories: 编程世界
---
最近有关折叠屏产品的新闻层出不穷，各家手机厂商也分别慢慢地亮出了自家的产品。然而市场上的一些APP仍然没有很好地适配这样的设备，显示不正常和应用重启的状况时有发生。因此，我会用接下来的几篇文章来点出有关折叠屏开发中的一些需要注意的地方。  
今天我们先来说一下**生命周期**，这是广大开发者特别需要注意的一点。  
首先我们来看一下测试代码：  
```
public class MainActivity extends AppCompatActivity {

    private final String TAG = "MainActivity";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Log.d(TAG, "onCreate");
    }

    @Override
    protected void onStart() {
        super.onStart();
        Log.d(TAG, "onStart");
    }

    @Override
    protected void onResume() {
        super.onResume();
        Log.d(TAG, "onResume");
    }

    @Override
    protected void onPause() {
        super.onPause();
        Log.d(TAG, "onPause");
    }

    @Override
    protected void onStop() {
        super.onStop();
        Log.d(TAG, "onStop");
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        Log.d(TAG, "onDestroy");
    }

    @Override
    protected void onRestart() {
        super.onRestart();
        Log.d(TAG, "onRestart");
    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        Log.d(TAG, "onSaveInstanceState");
    }

    @Override
    protected void onRestoreInstanceState(Bundle savedInstanceState) {
        super.onRestoreInstanceState(savedInstanceState);
        Log.d(TAG, "onRestoreInstanceState");
    }

    @Override
    public void onConfigurationChanged(Configuration newConfig) {
        super.onConfigurationChanged(newConfig);
        Log.d(TAG, "onConfigurationChanged");
    }

}
```
我在每一个生命周期和恢复现场的回调方法中都加了Logcat输出，我们来看一下切换屏幕时APP的具体表现。
>2019-01-30 11:19:00.216 30205-30205/com.example.helloworld D/MainActivity: onPause  
2019-01-30 11:19:00.221 30205-30205/com.example.helloworld D/MainActivity: onSaveInstanceState  
2019-01-30 11:19:00.227 30205-30205/com.example.helloworld D/MainActivity: onStop  
2019-01-30 11:19:00.228 30205-30205/com.example.helloworld D/MainActivity: onDestroy  
2019-01-30 11:19:00.325 30205-30205/com.example.helloworld D/MainActivity: onCreate  
2019-01-30 11:19:00.326 30205-30205/com.example.helloworld D/MainActivity: hashcode is 89642980  
2019-01-30 11:19:00.327 30205-30205/com.example.helloworld D/MainActivity: onStart  
2019-01-30 11:19:00.328 30205-30205/com.example.helloworld D/MainActivity: onRestoreInstanceState  
2019-01-30 11:19:00.331 30205-30205/com.example.helloworld D/MainActivity: onResume  

我们可以看到，**默认情形下，Activity在屏幕尺寸发生改变的时候也随之重启了。** 和改变屏幕Orientation的行为有几分相像。  
此时我们面临两个选择：
1. 重启APP以适应屏幕改变；  
2. 手动处理数据，避免APP重启。  

对于第一种情况，我们一般在回调onSaveInstanceState()方法中保存数据，并在onCreate()或onRestoreInstanceState()回调方法中取回保存的数据用于恢复现场。  
然而，并非在所有情况下都需要完整地重启APP来适应屏幕改变，和改变屏幕方向一样，我们只需要在Androidmanifest.xml中相应的Activity声明中加入相应的属性值即可。此处，需要添加：  
```
android:configChanges="screenSize|smallestScreenSize|screenLayout"
```
添加好之后再次运行APP并反复改变屏幕大小，此时的生命周期回调顺序变为：
>2019-01-30 11:13:02.217 29276-29276/com.example.helloworld D/MainActivity: onCreate  
2019-01-30 11:13:02.219 29276-29276/com.example.helloworld D/MainActivity: onStart  
2019-01-30 11:13:02.223 29276-29276/com.example.helloworld D/MainActivity: onResume  
2019-01-30 11:13:05.289 29276-29276/com.example.helloworld D/MainActivity: onConfigurationChanged  

可见，此时Activity并没有销毁重建，而是只回调了onConfigurationChanged()方法。在某些情况下，可用此办法避免APP重启。  
那么，上述三个属性值各代表什么意思呢，详见下表：  

| 属性值 | 含义 |
| ----- | ---- |
|screenSize|当前可用屏幕尺寸发生了变化。它表示当前可用尺寸相对于当前纵横比的变化，因此会在用户在横向与纵向之间切换时发生变化。 不过，如果您的应用面向 API 级别 12 或更低级别，则 Activity 始终会自行处理此配置变更（即便是在 Android 3.2 或更高版本的设备上运行，此配置变更也不会重新启动 Activity）。|
|smallestScreenSize|物理屏幕尺寸发生了变化。它表示与方向无关的尺寸变化，因此只有在实际物理屏幕尺寸发生变化（如切换到外部显示器）时才会变化。 对此配置的变更对应于smallestWidth 配置的变化。 不过，如果您的应用面向 API 级别 12 或更低级别，则 Activity 始终会自行处理此配置变更（即便是在 Android 3.2 或更高版本的设备上运行，此配置变更也不会重新启动 Activity）。|
|screenLayout|屏幕布局发生了变化 — 这可能是由激活了其他显示方式所致。|
注：上表摘自 https://developer.android.com/guide/topics/manifest/activity-element  
如此，我们便处理完了对于折叠屏切换屏幕的优化。
