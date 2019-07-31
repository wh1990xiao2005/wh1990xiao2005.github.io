---
title: Activity与DialogFragment交互的方法
date: 2015-10-27 20:23:49
tags: Android
categories: 编程世界
---

好久没有更新博客了，今天我们来讨论一下如何在Activity与DialogFragment交互的方法，这里包括了DialogFragment的启动以及Activity方法的调用。   
DialogFragment与Dialog相比类似，是官方现在更建议使用的Dialog。   
**DialogFragment的定义**  
新建一个DialogFragment，该类继承DialogFragment。复写onCreateView()方法，在该方法中设置Dialog的布局。这个Dialog的布局可以完全自定义，可以包括任何常见的河自定义的控件。  
下面是一个实例：  

```
@Override
public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {  
    getDialog().requestWindowFeature(Window.FEATURE_NO_TITLE);
    View layoutView = inflater.inflate(R.layout.fragment_dialog_login_tip, container, false);     
```

如上代码块所示，fragment_dialog_login_tip.xml里面我只定义了一个TextView显示一些信息。如果读者想要添加按钮或其他控件，可在这个复写的方法中通过layoutView.findViewById()来初始化并设置监听器。  
**DialogFragment的启动**  
要启动一个DialogFragment，要在Activity中new出该类的实例，然后通过.show()方法启动。    
下面是一个实例：  

```
FragmentManager fragmentManager = getSupportFragmentManager();
loginTipDialogFragment = new LoginTipDialogFragment();
loginTipDialogFragment.setCancelable(false);
loginTipDialogFragment.show(fragmentManager, "login_tip_dialog");
```

如上所示，这个DialogFragment就可以显示出来了。  
**从DialogFragment调用Activity中的方法**  
要从DialogFragment调用Activity中的方法，首先满足下列两点要求：  

	 1. 该Activity是启动该DialogFragment的；  
	 2. 要被调用的方法是public的。

比如，在上面那个启动DialogFragment的Activity中有一个exit()方法，用来退出应用程序，Activity的名字叫做TestActivity。DialogFragment中要实现退出程序可以按照如下方法发起调用：  

```
((TestActivity)getActivity()).exit();
```

**从Activity 调用DialogFragment中的方法**  
这种方式的调用就简单多了。由于有对象实例，直接 .方法名 就可以了。