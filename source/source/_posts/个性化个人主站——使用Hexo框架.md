---
title: 个性化个人主站——使用Hexo框架
date: 2015-07-02 13:55:23
tags: Web
categories: 编程世界
---

开门见山，之前我们有谈过使用Github来搭建免费的个人博客站点，从而摆脱现有博客提供商的限制，实现对博客内容的完全自定义。今天我们借用Hexo的框架来搭建站点。

关于搭建的过程，可以参考这位仁兄的博文：  
[http://ibruce.info/2013/11/22/hexo-your-blog/](http://ibruce.info/2013/11/22/hexo-your-blog/)   
还有Hexo框架的网站（下载安装Hexo会用到）：  
[http://hexo.io/](http://hexo.io/)   
我在这里依旧是给大家的建站过程提供一些提示。  


**Node.js**   
关于Node.js呢，如果大家使用Windows，而且是下载的exe安装程序安装的话，即使在安装程序里面选中了加入环境变量，在命令提示符中敲npm指令依旧不起作用。起码在单位的Windows 7和我自己电脑的Windows 8.1平台上是这样。解决之道是打开系统的环境变量设置，输入一个空格，再去掉这个空格，确定生效一下即可。~囧~  


**关于本地化和个性化**  
其实，Hexo官方给出了多语言的解决方案，在框架根目录的\_config.yml中进行配置就好。但是当我一旦在language字段写入CN之后就会在生成过程中报错，不知为何。   
因此我就绕道而行，直接去改themes中的主题文件了。这里列举一些（以默认landscape为例）：  
在themes\landscape目录下，也有一个叫做\_config.yml文件。打开它，在menu组下有几对对应的关系，前面的"Home"之类的英文，这些对应与网站左上角的栏目，把它改成中文的吧。比如：首页、归档……此外，这里支持扩展，比如你可以加上：  
博客: http://blog.csdn.net/xwhnew  
当访客点击博客时，就会自动跳转到上面的网页了。  
Content注释下的excerpt_link是博文的概览后的“Read more”文字内容，将其改为“阅读更多”即完成汉化。  
Miscellaneous注释下的favicon是网站的图标，不多说了，个性化必须改的！  
在themes\landscape\layout\_widget下各个文件中，widget-title记得改为中文，比如archive是归档。  
themes\landscape\source\css\images路径下的banner.jpg，是网站上方的背景图，换成自己喜欢的吧！  

**有关博文移植**  
相信不少朋友希望把自己的所有博文都移植到个人站点中。想法不错，实现起来呢，基本掌握Markdown语法是必须的；其次，要有一个比较好的编辑器，网上有在线版，也有软件。这里比较推荐MarkdownPad，是一个PC端软件，无需购买付费版即完全够用，我现在也在用。最后，Hexo是根据.md文件的头部信息来读取日期信息的，如果我们是移植之前的博文，只需在date后将当前的时间改为旧博文发布的时间即可。