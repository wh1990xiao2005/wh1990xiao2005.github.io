---
title: 自定义ImageView系列 － 区域截图（上）
date: 2015-11-04 23:52:48
tags: Android
categories: 编程世界
---

## 功能要点：
1. 根据控件自身大小计算合适的透明正方形预览区；
2. 截取预览区图像并按照指定的尺寸缩放，生成Bitmap对象。
本文着重介绍上述第1个要点。

博主又来更新文章啦！  
关于ImageView呢，其实我之前接触过一些继承它来实现一些功能。比如双指缩放，单指移动之类。而最近工作中又要用到继承它来实现某些功能。借此机会好好整理一下，也分享给更多需要的朋友们！  
使用本系列文章完成的自定义多功能ImageView可以实现微信中的头像选择的照片调整功能。而且，微信的图片调整界面会有不流畅的情况出现。而用本文中的办法是不会出现卡顿现象的。当然，这可能是机器的原因，不同的机器可能表现不同。我使用的测试机是三星的S6。  
废话不多说，我们来讲一下如何绘制中间的透明框。  
思路是这样的：为了更好的可移植性，首先通过获取ImageView的尺寸，获得ImageView的尺寸；然后通过该尺寸计算出控件中点和合适的正方形边长，一般是由宽和高其中最短的一者决定的；最后，将该正方形外的所有部分给一个半透明的黑色，再画出正方形的边缘即可。
获取控件的高度不是难事，继承了ImageView后，调用getWidth()和getHeight()方法即可完成宽高的获取。   
获取控件的中点的方法是首先获取控件所在位置的起始点，也就是左上角的x和y的绝对像素坐标点。然后与宽度和高度分别相加，就可得到控件的终止点，也就是控件右下角的x和y的绝对像素坐标点。有了这两个值，控件的中点就可以确定下来了。  
下面再定义预览框的边长。由于这里我们要采用绝对像素值，因此需要根据控件的大小动态计算出预览框的大小。这里我要提醒各位的就是：一个正方形，只要有两个完整的x和y轴的组合坐标，就可以确定该正方形的位置了，即对角坐标。我们这里计算左上和右下的位置。  
左上角x坐标我是采用了中点x坐标减控件起点x位置再除以8确定的。换言之，就是该值是整个预览框距离左边的距离，而又由于这是一个正方形，因此右上角的坐标也就同时确定了（中点坐标x减去左上角x坐标再乘以2）。y轴坐标也很容易了，只需用中点y坐标减去上述两个x轴坐标之差的二分之一就可以了。由此，y轴终点的坐标也可以计算出来了，即用起点y轴坐标加上两个x轴之差坐标即可。

**特别注意 － 这里有坑**  
后面我会把这部分的代码贴上，个人私心想，肯定会有人直接复制粘贴，所以……必须提醒一下各位：犹豫项目需求问题，我这里只考虑了控件高度大于控件宽度的情况，所以，诸位在复制粘贴后，务必判断一下宽高的情况，然后分情况讨论。  
上述问题分别出现在中点和预览区边距的计算上。  
如果这个坑的问题没看懂，没事，试一次就知道了。方法是：不要限制屏幕旋转，然后整个屏幕放这样一个ImageView，然后倒转屏幕，看看正方形会发生什么变化。  
**祝君好运！**  
最后，我们画正方形的边缘，再在其余的部分给定半透明黑色即可。这里就直接放代码片段了：  
画正方形边缘：  

```
if (previewEdge != null) {
    //上
    canvas.drawLine(previewEdge[0], previewEdge[2], previewEdge[1], previewEdge[2], rectPaint);
    //左
    canvas.drawLine(previewEdge[0], previewEdge[2], previewEdge[0], previewEdge[3], rectPaint);
    //下
    canvas.drawLine(previewEdge[0], previewEdge[3], previewEdge[1], previewEdge[3], rectPaint);
    //右
    canvas.drawLine(previewEdge[1], previewEdge[2], previewEdge[1], previewEdge[3], rectPaint);
｝
```

解释一下：该部分代码放在重写的onDraw()中。previewEdge是一个有4个int值的数组，分别代表左上和右下的x和y坐标值。稍后贴出计算部分代码。  
填充半透明黑色：  

```
//灰色部分
canvas.drawRect(0, 0, previewEdge[0], myLocationEnd[1], rectBgPaint);
canvas.drawRect(previewEdge[0], 0, previewEdge[1], previewEdge[2], rectBgPaint);
canvas.drawRect(previewEdge[1], 0, myLocationEnd[0], myLocationEnd[1], rectBgPaint);
canvas.drawRect(previewEdge[0], previewEdge[3], previewEdge[1], myLocationEnd[1], rectBgPaint);
```

各种计算的代码片： 

```
//获取控件自身起始点
myLocationStart = new int[2];
getLocationOnScreen(myLocationStart);
//获取控件自身终止点
myLocationEnd = new int[2];
myLocationEnd[0] = getWidth() + myLocationStart[0];
myLocationEnd[1] = getHeight() + myLocationStart[1];
//计算控件自身中点
myLocationMid = new int[2];
myLocationMid[0] = (myLocationStart[0] + myLocationEnd[0]) / 2;
myLocationMid[1] = (myLocationStart[1] + myLocationEnd[1]) / 2 - myLocationStart[1];
//设置中间预览框边框色
rectPaint = new Paint();
rectPaint.setColor(Color.WHITE);
rectPaint.setStrokeWidth(2);
rectBgPaint = new Paint();
rectBgPaint.setColor(getResources().getColor(R.color.photo\_adjust\_for\_avator\_bg));
//计算中间预览边框位置
previewEdge = new int[4];
previewEdge[0] = (myLocationMid[0] - myLocationStart[0]) / 8;
previewEdge[1] = myLocationEnd[0] - previewEdge[0];
previewEdge[2] = myLocationMid[1] - ((previewEdge[1] - previewEdge[0]) / 2);
previewEdge[3] = myLocationMid[1] + ((previewEdge[1] - previewEdge[0]) / 2);  
```

变量的名字都很容易看懂吧，我这里就不详述了。  
最后说一句，这个最好自己练习一下计算，印象深一些。完整的ImageView类代码将在整个系列文章的最后一篇中贴出。希望大家多多支持，谢谢！！！

