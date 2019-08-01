---
title: 粮草先行——Android折叠屏开发技术点番外篇之运行时变更处理原则
date: 2019-02-14 11:33:28
tags: Android
categories: 编程世界
---

上一篇文章中，我们有提到Activity在屏幕尺寸发生变更时的处理方式，总共有两种：

 1. 重启APP以适应屏幕改变；
 2. 手动处理数据，避免APP重启。

同样，这两种方式也同时适用于改变屏幕方向、更改系统语言、甚至输入法等等。
因此，本文也同样**适用于改变屏幕方向等情况的处理。**
或许你会有疑问：**我们该如何选择合适的处理方式呢？**
我给你的答案是：**选择最合适的。**  
这么说好像跟没说一样，别急，给大家举个例子就明白了：
比如更改屏幕方向，由竖屏转换为横屏，如果我们只有一套布局，符合按比例缩放仍然显示正常的话，我们大可以选择第2种处理方案。但是如果我们的横竖屏布局是不同的，比如系统中的“设置”应用，那么我们选择第2种处理方案就是不合适的。
下图：
![横竖屏采用不同布局的样例](https://img-blog.csdnimg.cn/20190214110923934.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3doMTk5MHhpYW8yMDA1,size_16,color_FFFFFF,t_70)
这是一个典型的横竖屏分别采用不同布局的例子。
我们确定要采取那种解决方案后，接下来很可能要面对另一个问题，就是性能瓶颈。
根据前一篇文章的实验结果，在发生横竖屏切换的时候，Activity的生命周期通常会按照如下顺序依次执行：

> D/MainActivity: onPause   
D/MainActivity: onSaveInstanceState  
D/MainActivity: onStop  
D/MainActivity: onDestroy  
D/MainActivity: onCreate  
D/MainActivity: onStart  
D/MainActivity: onRestoreInstanceState  
D/MainActivity: onResume  

如果我们在生命周期中做了一些繁重的工作，那么整个Activity在重启的过程中就会很慢。
要解决这个问题，首先我们要找Fragment帮忙，因为Bundle并不是用来传递大型对象的，而且这个对象还需要序列化和反序列化，如此执行起来就更慢了。
当然，如果你只是保存一些整型或者字符串的话，单纯使用Bundle而不借助Fragment也是可以的，但是这样的场景在实际开发中并不常见。
要借助Fragment来中转对象，我们采用下面三步走的方式：

 1. 在Fragment类中调用 setRetainInstance(true)；
 2. 在Activity销毁时向Fragment类存入数据；
 3. 在Activity重建后根据Tag检索Fragment，并取出之前存入的数据。

下面用具体的代码片来演示：
首先来看Fragment类：

```
public class TestFragment extends Fragment {

    private MyData data;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setRetainInstance(true);
    }

    public void setData(MyData data) {
        this.data = data;
    }

    public MyData getData() {
        return data;
    }
}
```
我们再来看Activity类：

```
public class MyActivity extends Activity {

    private TestFragment mTestFragment ;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        FragmentManager fm = getFragmentManager();
        mTestFragment = (TestFragment)fm.findFragmentByTag(“test”);

        if (retainedFragment == null) {
            mTestFragment = new TestFragment ();
            fm.beginTransaction().add(mTestFragment, “test”).commit();
            mTestFragment.setData(restoreData());
        }
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        mTestFragment.setData(saveData());
    }
}
```
这里还要特别注意一点：在中转对象数据时，不要传入与Activity紧密相关的对象，比如View，否则会造成内存泄漏。
至此，就完成了对重启Activity方案的性能优化。