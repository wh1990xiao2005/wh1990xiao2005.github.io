---
title: Android平台录音音量计的实现
date: 2015-07-08 13:31:50
tags: Android
categories: 编程世界
---

今天博主要给大家分享的是如何在Android平台上实现录音时的音量指示计。开门见山，先来看一张Demo的效果图：   
![Demo样例](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTUwNzA4MTMyNTE5NzY3)
如上图所示，两个按钮分别是开始录音和停止录音，中间的两个数字前后分别代表音量档位（目前是8档）和原始音量值（0-32767），下方是音量计，由ProgressBar负责显示。   
完整的源码可以在下面的网址找到：   
https://github.com/XiaoWenHan/AndroidMircrophoneVolumeDemo     
欢迎各位读者批评指正，谢谢！