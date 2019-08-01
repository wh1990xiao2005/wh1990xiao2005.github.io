---
title: IntelliJ IDEA 14 值得你弃用Eclipse的IDE神器
date: 2014-11-06 19:42:33
tags: Java
categories: 编程世界
---

今天看新闻，发现IntelliJ IDEA版本出了14，本着生命在于折腾的原则，下载并安装。   
当然，是开玩笑的啦！作为比较资深的Android开发者，相信朋友们不会不知道Android Studio这个软件。它就是基于IntelliJ IDEA（以下简称IDEA），所以借着新版本的发布，看看最原始的IDEA是个什么模样。   
注：大家一定要有信心，这款IDE我上手的时间仅仅用了2个小时不到，个人感觉还是比较适合我的。如果花上两个小时能够提高日后的编码效率，还是很值得的。   
IDEA分为两大版本，Ultimate（付费）和Community（免费）。大家可以搜索一下，他们的差距还是相当大的，主要区别在于对Web开发的支持。虽然我只是在Android平台上面开发，但是谁又说得准会不会写个JSP之类的，万一要写了呢？所以我使用了功能比较全面的Ultimate（收费软件在我国情况……大家了解的，可以去下载相关工具）。   
不出意外的话，首次运行会让用户选择使用的插件，默认是全开，一路下一步后显示下面的界面：   

![IDEA 14启动界面](https://img-blog.csdn.net/20141106191706001?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHdobmV3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

这里大家明确一下概念，往常在Eclipse中，有个“Workspace”的概念，我们会把很多项目放在一个工作空间中。到了idea中，完全不一样了。它的做法是，一个Project对应一个Module，一个Module对应我们的一个项目。但是不推荐一个Project包含多个Module，因为默认情况下输出目录之类的文件会放在一起，这也是官方不建议的。简而言之，就是一个Project对应一个项目，没有Workspace的概念。   
下面我们创建android应用程序，在此之前要创建一个Project，类型为Android。   
![新建项目](https://img-blog.csdn.net/20141106192220312?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHdobmV3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

大家注意看左边的类型，免费版会少很多，真的是很多。所以再次提醒一下，防患于未然，万一要用呢。   
下一步后和Eclipse里面创建项目大体相当，不再多说。   

![创建项目](https://img-blog.csdn.net/20141106192406968?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHdobmV3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

下一屏幕我们需要指定一下使用的JDK和SAndroid SDK版本，注意这里需要选择兼容的最低版本。单击“New...”之后，我们依次进行选择：   

![填写必要的信息](https://img-blog.csdn.net/20141106192718692?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHdobmV3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

上图中，最后一项是运行选项，我们保持默认（运行时选择要运行该应用的设备）。   
完成后需要一段时间的等待，然后我们就可以顺利进入工作环境了。   

![工作区](https://img-blog.csdn.net/20141106193148781?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHdobmV3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

我们可以发现，界面整体感觉还是很清晰、简洁的。   
要运行HelloWorld，需要Shift+F10即可。    
此外，对于Android的布局，有了实时预览功能：   

![实时预览](https://img-blog.csdn.net/20141106193436984?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHdobmV3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)   

这一点在Eclipse上面是不太好实现的，起码我没有找到这个功能，而这一功能可以说是广大Android开发者的福音。   
不仅如此，对于不建议使用的控件，在设计视图中也有明确的提示：    

![废弃的控件提示](https://img-blog.csdn.net/20141106193535350?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHdobmV3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)   

另外，诸位如果使用过最新版本的Eclipse Luna的话，可以发现有全黑主题了，但个人感觉Idea的全黑主题更好一些。这个大家可以从File->Settings->Appearance % Behavior->Appearance中选择Theme为Darcula。另外，还支持全屏显示（View->Enter Full Screen），这样一来，全屏都会是暗色系。   

![Darcula主题的IDEA](https://img-blog.csdn.net/20141106194006140?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHdobmV3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

以上给广大开发者，尤其是Android开发者一个IDE的介绍，上手不难。用久了Eclipse后，换一个风格迥异的IDE也是一个不错的选择。最后希望上述内容能够提供一些比较实用的参考价值。