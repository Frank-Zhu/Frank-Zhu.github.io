---
layout: post
title: Android开发环境搭建(AndoirdStudio/IDEA)
tags: [tools]
categories: [android]
---

##一、 介绍

------
Android Studio是Google新发布的Android应用程序开发环境，Android Studio是基于IntelliJ IDEA开发而成的。在Android Studio开发环境中，Android开发者可以非常方便地调试Android应用程序，Android Studio不仅是Android开发的IDE集成环境，而且还有以下特点：

- **构建方便** ：基于 Gradle 的构建支持

- **强大的IDE** ：Android 专属的重构和快速修复

- **功能丰富** ：提示工具(专属插件)以捕获性能、可用性、版本兼容性等问题

- **编译简单** ：多版本多渠道超级方便，相信用过Eclipse的ANT构建环境的就知道多版本多渠道是有多麻烦，门槛有多高了，有了Gradle之后，多版本多渠道就是浮云了。（想知道ANT多渠道编译的可以看我这篇博客[ANT多渠道打包](http://frank-zhu.github.io/2014-05-23-android-ant.html)）

- **模板超多** ：基于模板的向导来生成常用的 Android应用设计和组件

- **功能强大的布局编辑器** ：可以让你拖拉 UI控件并进行效果预览

##二、文件准备


1、[Android Studio下载安装](http://developer.android.com/sdk/installing/studio.html)可以去官网下载最新版本，现在是V0.8.6(注意选择适合自己的系统版本X86/64位)

2、[JDK环境安装下载](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)注意下载和自己系统匹配的版本，不然会导致AndroidStudio会找不到JDK环境的。

3、一把好用的梯子。
>在天朝想使用谷歌的服务，你懂得！所以一把好用的梯子会事半功倍，特别是对于第一次启动AndroidStudio的时候，等会你就直到有个好的梯子是多么的重要。什么，你说再哪里找梯子，这种问题不要问我，问了我也不会告诉你的~~~


##三、软件安装

1、先把之前下载好的JDK软件默认安装，一路NEXT，直到完成

2、配置JAVA环境变量控制面板→系统→高级系统设置→“高级”下的“环境变量”：

    * 我的电脑->属性->高级->环境变量
    * 系统变量新建JAVA_HOME，变量值填写JDK的安装路径，在这里就填写“C:\Program Files\Java\jdk1.7.0_21”了
	* 在系统变量里点击新建变量名填写CLASS_PATH，变量值填写‘.;%JAVA_HOME%\lib;%JAVA_HOME%\lib\tools.jar’；
    * 在系统变量里找到Path变量，这是系统自带的，不用新建。双击Path，由于原来的变量值已经存在，故应在已有的变量后加上“;%CLASS_PATH%;%JAVA_HOME%;”注意前面的分号
3、安装之前下载的最新版本AndroidStudio，如果你的系统是64位的那就一路NEXT默认安装就好，但是如果你的系统是
32位的，那么很有可能会遇到下面的问题,下面给出解决方案：

```
都是针对执行studio.bat出现错误导致android studio运行不起来，最后都是异工同曲：

解答下32位 win7系统的问题。
在安装目录中找到bin下的studio.bat，用编辑打开studio.bat，第25到28行：

SET JRE=%JDK%
IF EXIST "%JRE%\jre" SET JRE=%JDK%\jre
SET BITS=
IF EXIST "%JRE%\lib\amd64" SET BITS=64
可以看到里面设置软件支持系统位数是64位，而我自己所用电脑是32位的 jre/lib目录下只有i386文件，尝试把

IF EXIST "%JRE%\lib\amd64" SET BITS=64
改为
IF EXIST "%JRE%\lib\i386" SET BITS=32
对于32位的xp系统：
SET BITS=   改为 SET BITS=32
```

##四、新建HelloWorld工程
1、双击AS，打开软件，启动之后你会看到以下界面

---------
![Start Screenshot][01]

2、这里我们选择第一个，新建一个工程，填上名称,然后点击Next
![New First Step Screenshot][02]

3、选择我们的APP工程的MinSDK版本号，这里用红框框出来就是谷歌统计的各个Android版本的市场占有率，可以看到4.0及以上的已经占到了84.3%了，而2.2及以上的占到97%了，所以这里根据自己的项目需要去选择APP支持的最低版本，现在很多APP都是直接4.0及以上的，放弃了老旧版本的支持，当然，这个是需要根据本身的项目来决定支持的最低版本。
![New Second Step Screenshot][03]

4、选择完支持的最低版本，现在就来选择我们应用启动的第一个activity吧，可以看到，studio提供了很多的模板可供选择，由于现在用Fragment比较普遍，所以这里我们选择第三个，然后点击NEXT。
![New Three Screenshot][04]

5、可以看到，在这个页面我们可以自定义我们的启动activity名称以及layout的名称，可以指定默认activity的Title名称，这里我们直接使用默认的，然后点击Finish完成。
![New Four Screenshot][05]

6、好了，到了这一步，IDE已经帮我们自动生成了一个叫HelloWorld的工程。但是，如果你是第一次编译，Gradle会需要下载一些Gradle本身的程序包，由于我们是在天朝，所以就像我上面所说的“一把好梯子”是很重要滴，因为很多人就是到这一步就一直卡在这里啊，就没进去过，一直是显示在下载，这个时间那是相当的漫长啊。
![New Four Screenshot][06]

7、好了，如果你看到了下面这个界面，那么恭喜你，已经搭建好了你的安卓开发环境，而且也编写了你的第一个HelloWorld程序，什么，你说你什么也没干，就已经写好程序了？我不是还没写代码么？这不科学！对的，所有的事AndroidStudio都帮我们做好了，这个就是一个可以直接运行的程序。

    1）我们可以看到我标示出来的数字，左边数字1的区域就是项目工程区，这里有一个需要解释一下的就是，AndroidStudio和Eclipse的区别，那就是project和module，这里的project就类似于Eclipes的WorkSpace,而module就是Eclipse里面的project了，只要搞清楚这个关系就好了，也就是如果你要新建个项目，可以直接新建一个module就可以了，不需要新建一个project。
    
    2）数字2的区域就是代码编写区了，在这里我们可以编辑我们的代码，可以看到系统已经帮我们生成了一个layout文件。
    
    3）这个就是预览设备的选择区域了，左边可以选择预览的设备，然后是横竖屏支持，然后是App的预览主题，最后是可以选择预览的SDK版本。
    
    4）第四个当然就是布局效果预览了，可以很方便的看到布局代码的最终效果。

![New Four Screenshot][07]

8、我们点开设备选项，可以看到IDE为我们提供的一些预览设备，可以看到都是一些谷歌亲儿子的设备。可以看到我红框框出来的部分，点击最后一项就可以看到所有设备的预览效果了。

![New Four Screenshot][08]

这就是所有设备预览的效果了，怎么样，是不是很酷炫？

![New Four Screenshot][09]

8、好了，讲完这个我们来看一下安卓开发过程中主要用到的几个地方。
![New Four Screenshot][10]

    1）IDE的设置按钮，这个我们等会讲到
    2）工程的设置按钮
    3）Gradle同步按钮，这个按钮点了之后会自动帮你下载需要用到的包文件
    4）DDMS，设备信息，可以做一些操作，比如debug、操作SD卡啊什么的
    5）这个是SDK Manager
    6) 这个是安卓虚拟机，这里不推荐使用，因为实在是太慢太慢了，后面会推荐一款最快的模拟器

9、我们点开SDK Manager，在这里进行SDK的下载和管理，一般下载IDE的时候会默认带上最新的SDK版本，如果你需要其它的版本，就需要自己单独下载了。PS:这里也是需要搬梯子翻墙滴，不然你就等吧，很可能半小时还下载不了5M，简直就是龟速。当然你如果没有VPN，也可以通过修改HOST和更改HTTPS访问方式为HTTP，会快很多。
![New Four Screenshot][11]

10、好了，讲完这些，你已经跃跃欲试的想运行一下APP？OK，那你就直接点击设置按钮边上的那个绿色的小三角运行吧。PS：快捷键是Alt+Shift+X

11、到上面，基本上你把你的APP跑起来肯定是没有什么问题了。接下来我们讲讲IDE的设置，这个也是很重要的啊，毕竟有一把好用的刀，还是会达到事半功倍的效果滴。我们点开设置按钮会看到下面的这张图，
![New Four Screenshot][12]
没错，我们就是要更改IDE的文件编码格式，全部改成UTF-8格式的，我可不想因为中文的问题导致全部乱码。更改编码格式以一个好的习惯，你也不想你用着GBK编码，你同事因为用的UTF-8编码导致中文全部乱码而破口大骂吧。所以在编码开始之前就把编码格式设置好是很重要滴。

12、接下来就是快捷键问题了，对于刚接触Studio的人来说，默认快捷键还是比较逆天的，特别是对于习惯了eclipse快捷键的人来说，那就不是一般的难用了，最初我也是习惯了好一阵才习惯过来。好在我们可以修改默认快捷键方式为eclipse，虽然还有一些快捷键还是没有，但是整体还是要舒服很多了，设置方式可以看我下面这张图就可以了。
![New Four Screenshot][14]

13、插件，没错用惯了eclipse了的怎么能接受没有插件来辅助开发呢？但是有个不好的消息就是目前IDEA的插件还不是很多，远远不及eclipse的插件多，这方面还是有很长的路要走啊。但是有几个插件还是很不错的，可以自己安装一下。不如我框出来的部分。
![New Four Screenshot][13]


[01]: https://raw.githubusercontent.com/Frank-Zhu/Frank-Zhu.github.io/master/img/android_studio_20140822/andoird_studio_20140822_01.png
[02]: https://raw.githubusercontent.com/Frank-Zhu/Frank-Zhu.github.io/master/img/android_studio_20140822/android_studio_20140822_02.png
[03]: https://raw.githubusercontent.com/Frank-Zhu/Frank-Zhu.github.io/master/img/android_studio_20140822/android_studio_20140822_03.png
[04]: https://raw.githubusercontent.com/Frank-Zhu/Frank-Zhu.github.io/master/img/android_studio_20140822/android_studio_20140822_04.png
[05]: https://raw.githubusercontent.com/Frank-Zhu/Frank-Zhu.github.io/master/img/android_studio_20140822/android_studio_20140822_05.png
[06]: https://raw.githubusercontent.com/Frank-Zhu/Frank-Zhu.github.io/master/img/android_studio_20140822/android_studio_20140822_06.png
[07]: https://raw.githubusercontent.com/Frank-Zhu/Frank-Zhu.github.io/master/img/android_studio_20140822/android_studio_20140822_07.png
[08]: https://raw.githubusercontent.com/Frank-Zhu/Frank-Zhu.github.io/master/img/android_studio_20140822/android_studio_20140822_08.png
[09]: https://raw.githubusercontent.com/Frank-Zhu/Frank-Zhu.github.io/master/img/android_studio_20140822/android_studio_20140822_09.png
[10]: https://raw.githubusercontent.com/Frank-Zhu/Frank-Zhu.github.io/master/img/android_studio_20140822/android_studio_20140822_10.png
[11]: https://raw.githubusercontent.com/Frank-Zhu/Frank-Zhu.github.io/master/img/android_studio_20140822/android_studio_20140822_11.png
[12]: https://raw.githubusercontent.com/Frank-Zhu/Frank-Zhu.github.io/master/img/android_studio_20140822/android_studio_20140822_12.png
[13]: https://raw.githubusercontent.com/Frank-Zhu/Frank-Zhu.github.io/master/img/android_studio_20140822/android_studio_20140822_13.png
[14]: https://raw.githubusercontent.com/Frank-Zhu/Frank-Zhu.github.io/master/img/android_studio_20140822/android_studio_20140822_14.png
