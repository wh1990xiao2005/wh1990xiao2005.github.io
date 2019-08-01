---
title: 搭建React Native环境的几个Tips
date: 2016-11-08 08:09:47
tags: React Native
categories: 编程世界
---

博主最近在研究跨平台开发，打算好好研究一下React Native这个比较被大家熟知的玩意。  
昨天进行了环境搭建和初步的学习，感觉上手还算快。在这里，把一些已经发现的坑，提前告诉给各位，希望能让读者们少走一些弯路。  
首先说一下无论是Mac，Windows，还是Linux，如果有能力，请直接去英文官网查看文档，不要看中文官网，真的有坑，稍候细说。  
我的环境，Mac，Windows都有，先说Windows。  
Windows环境暂且肯定是不支持开发iOS应用的哈，这个大家应该知道。然后大家按照官网文档中的步骤来就行。这里放一个链接：  
https://facebook.github.io/react-native/docs/getting-started.html 
这个给大家提个醒，最好全程备梯子翻墙。  
说下Chocolatey，比较简单的方法就是cmd里面执行语句，很快就行了，这个是前提，没有Chocolatey，就没有后面的步骤，所以不要被它卡住。   
这里放下cmd命令：   
```
powershell -NoProfile -ExecutionPolicy Bypass -Command "iex((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin  
```
网络畅通的话几秒钟就安装好了。   
接下来就是Node和Python，这个按照官网的指令来就可以了。还是那句话，网络畅通的话也是很快就好了。这里可能Python慢一点，多等一下就会好。  
再下来就是准备Android开发环境，什么SDK啊，IDE（Android Studio）啊都备好。SDK的话我建议大家，下全，如果硬盘空间足够，就做个全下载吧。   
再下来安装React Native Cli，这一步，关掉cmd窗口，再开个新的，才能使用新的环境变量，也就是npm正常工作。重新打开cmd窗口后，还是按照官网的指引，安装React Native Cli。这一步会有命令行版的进度指示，虽然不太美观，至少还算直观。  
再下来配置Android SDK环境变量，这一步必须配，否则再下来会报错。配置的方法依然按照官方文档走。  
设置完环境变量之后，就可以运行了，首先cmd进入一个文件夹，这个文件夹将作为项目代码文件夹，可以新建，这个就随意了。  
运行`react-native init AwesomeProject`后，会出现install若干包的提示，这个是正常的，而且仅当新建项目的时候会有这个步骤，日后重新运行就没有了，而且不可省略，因此这里多等待一下吧。  
若干分钟后，进入新项目目录下执行`react-native run-android`，代码就运行起来了，和在Android Studio里面按Shift+F10（默认的Windows环境下的“运行”快捷键）的效果是一样的。这一步在首次运行时也需要下载一堆东西，日后也是无需的。  
部署到手机上之后，按手机的菜单键，或`adb shell input keyevent 82`唤出菜单键效果，在`Dev Settings`项里面把PC端IP和端口设置好。具体方法参见：https://facebook.github.io/react-native/docs/running-on-device-android.html   
文档里有详细的说明，当然，中文文档在部署运行上给出的向导一样贴心。   
再来说下Mac平台。  
到Mac平台就简单多了，还是按照官网的指示就行，没什么好特别强调的。不过这里还是要给大家提个醒，如果你看的是React Native的中文官网，那么，在安装node之后，在推荐安装里你会发现推荐安装Watchman。这个必须装，不是选装组件，是必须的，否则电脑端JS Server会报错，手机也会红屏报错，切记！  