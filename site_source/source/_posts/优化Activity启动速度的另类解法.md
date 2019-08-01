---
title: 优化Activity启动速度的另类解法
date: 2018-07-29 12:48:45
tags: Android
categories: 编程世界
---
今天来给大家分享一个性能优化的经验，主要在Activity启动方面。   
众所周知，给用户即时的响应是增强移动设备用户体验的重要一环，而Activity在启动过程中，又会经历至少onCreate(), onStart(), onResume()这三个回调过程。而在这三个过程中，又会经历绘制界面、载入数据、恢复现场等等实际操作。这对于一个Activity的启动多少都是会产生影响的。  
通常意义上讲，我们去优化Activity的启动速度都是在上述三个回调中下工夫，觉得只要上述操作优化得足够好，就可以有一个良好的体验。包括我们在百度上面去搜索提升Activity启动速度也是如此。  

完整代码请见 [完整代码][1]   

接下来我们来看这个问题的另一个方面。下面先看一个动画：   

![SecondActivity](https://img-blog.csdnimg.cn/20190716105348545.gif)   
  
我们可以注意到，在点击了"Launch SecondActivity"后，界面卡住了几秒，才发生跳转。而SecondActivity本身什么都没有做，如下所示：   

```
public class SecondActivity extends Activity {
    
    private final String TAG = getClass().getSimpleName();
    
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_second);
        Log.d(TAG, "onCreate end");
    }
    
    @Override
    protected void onStart() {
        super.onStart();
        Log.d(TAG, "onStart end");
    }
    
    @Override
    protected void onResume() {
        super.onResume();
        Log.d(TAG, "onResume end");
    }
}
```

这是一个很极端的情况，在启动过程中的各个生命周期，都只做了输出Log的操作，但仍然发生了卡顿的情况。   
我们看在启动它的Activity中，我们做了什么：   

```
@Override
protected void onPause() {
    super.onPause();
    sleep();
}

// Sleep 5000 ms in main thread.
private void sleep() {
    if (needSleep) {
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

在onPause()方法中，我们做了一个5000ms的主线程延迟，用来模拟大量的主线程操作，我们发现，一旦在onPause中工作量太大，也会导致接下来启动的Activity启动延迟。这也解释了为什么我们反复优化即将启动的Activity，却收效不大的原因。    
而一旦onPause()方法中，不进行操作时，或者实际项目中操作不多时，接下来的Activity会启动得很快，参考下面的演示：    

![Launch FirstActivity](https://img-blog.csdnimg.cn/20190716105423384.gif)

希望通过上面的描述，能给各位读者提供另一个性能优化的方案。   
Demo APK 和源码下载：   
https://github.com/wh1990xiao2005/pfmStartActivity/releases/tag/1.0


  [1]: https://github.com/wh1990xiao2005/pfmStartActivity