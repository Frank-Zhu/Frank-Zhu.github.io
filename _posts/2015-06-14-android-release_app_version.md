---
layout: post
title: 安卓集成发布详解（一）
tags: [gradle]
categories: [android]
---

鉴于公司项目需要由专人维护APP版本的发布，所以需要搭建专门用于版本发布的环境，下面就来介绍发布环境的搭建（Gradle构建部分，ant构建部分参见这篇博客[Ant版本发布](http://frank-zhu.github.io/android/2014/05/23/android-ant/)）。

##一、软件准备

1.[Gradle](http://gradle.org/downloads/)可能需要翻墙，这里有墙内下载地址[百度网盘下载](http://pan.baidu.com/s/1hqja0fU)

2.[Android SDK](http://developer.android.com/sdk/index.html)同样需要翻墙，这里有墙内下载地址[AndroidDevTools](https://github.com/inferjay/AndroidDevTools)

3.[JAVA](http://www.java.com/en/download/)

4.[SourceTree](https://www.sourcetreeapp.com/download/)我们代码是用GIT管理，如果你们的代码是用SVN管理，则自己下载相应的客户端工具.

##二、软件安装
1）Gradle的安装只需要将上面下载的全部文件解压到任意目录待用，之后将这个地址下的**bin**文件夹路径加到对应的环境变量，之后在命令行运行**gradle -version**命令查看当前gradle的版本号，如果能够正确输出版本号则为成功。
>WIN7下环境变量添加方法，我的电脑→属性→高级系统属性设置→高级→环境变量，先新建一个用户变量**GRADLE_HOME**填入你上面解压的路径，之后在系统**PATH**变量下添加**GRADLE_HOME/bin;**确定保存即可，重启命令行，然后运行**gradle -version**命令查看当前gradle的版本号.
>MAC下添加环境变量方法请自行谷歌，主要就是在**~/.bash_profile**文件添加相应路径即可，一定要注意路径不要填错了。

2)Android SDK的安装和Gradle的安装类似，解压到任意目录，然后添加以下几个路径到环境变量中即可**~/tools**和**~/platform-tools**其中**~**为你上面解压的SDK目录。
>WIN7下可以新建一个用户变量**ANDROID_SDK_HOME**填入你上面解压的路径，之后在系统**PATH**变量下添加**ANDROID_SDK_HOME/tools;ANDROID_SDK_HOME/platform-tools;**确定保存即可，重启命令行，然后运行**adb -version**命令查看当前ADB的版本号.
>MAC下添加环境变量方法请自行谷歌，和上面方法一样.

3)JAVA的安装，根据下载的EXE文件一路下一步默认安装即可，安装完成之后要添加以下环境变量，**注意添加的路径是JDK路径不是JRE路径**，添加方法和上面类似。
>WIN7下可以新建一个用户变量**JAVA_HOME**填入你上面解压的路径，之后在系统**PATH**变量下添加**JAVA_HOME/jdkxxx/bin;**注意**jdkxxx**要换成你自己的对应目录，确定保存即可，重启命令行，然后运行**adb -version**命令查看当前ADB的版本号.
>MAC下添加环境变量方法请自行谷歌，和上面方法一样.

4)SourceTree的安装，默认安装即可，如果需要注册授权可以填写邮件获取注册协议，根据提示做相应操作即可，这个是免费的。

**一定要注意路径不要填错了**

**一定要注意路径不要填错了**

**一定要注意路径不要填错了**

重要的事情说三遍

##三、版本编译
如果上面的软件都安装成功了，那接下来就是代码拉取和版本编译了。

1)代码拉取
>打开安装的SourceTree,点击新建Clone一个远程仓库，URL填入开发提供的代码地址，目标路径填入代码保存的路径即可，名称随便填写，最好是填写项目名称，填好以后点克隆按钮，然后去喝杯咖啡等代码拉取完毕。

2)代码编译
>第一步已经把代码拉取下来了，接下来就是准备编译了，在编译之前先来了解一下项目的代码结构。

可以看到项目根目录下有一个**build.gradle**文件，打开之后一般是下面这样的

{% highlight ruby %}

// Top-level build file where you can add configuration options common to all sub-projects/modules.

buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:1.0.0'

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

allprojects {
    repositories {
        jcenter()
    }
}

{% endhighlight %}

其中**com.android.tools.build:gradle:1.0.0**即为Gradle插件的版本。

然后在主模块目录下还有一个**build.gradle**文件，一般是在**app/build.gradle**也有可能你没有APP目录，具体可以问下你们的开发，你们的项目模块名叫啥，文件就在那个模块下，这个**build.gradle**文件才是版本编译的主角，它一般长这样

{% highlight ruby %}

apply plugin: 'com.android.application'

android {
    compileSdkVersion 21
    buildToolsVersion "22.0.1"

    defaultConfig {
        applicationId "com.ecloud.android.xreap"
        minSdkVersion 14
        targetSdkVersion 21
        versionCode 1
        versionName "1.0"
    }
    buildTypes {
        debug {
        }

        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.android.support:appcompat-v7:22.0.0'
    compile 'com.android.support:cardview-v7:21.0.3'
    compile 'com.android.support:recyclerview-v7:21.0.3'
}

{% endhighlight %}

当然你也有可能看到的不太一样，但主体应该是一致的，上面的部分缺少签名的代码，这个具体的你们开发一般会写好代码，你只需要执行一下命令即可。

下面就是今天版本编译最重要的部分了，其实也是最简单的部分。

打开命令行或者终端，将路径切换到代码根目录下，然后执行**gradle clean**命令，看到BUILD SUCCESSFUL则代表成功，然后执行**gradle assembleRelease**命令，看到BUILD SUCCESSFUL则代表版本编译成功，编译完成的APK文件在项目模块目录下的**build/outputs/apk/**路径下，正常情况下你可以看到生成的两个APK文件，一个是未签名版本一个是签名版本，其中签名版本就是我们最终要发布的版本。

以上

关于build.gradle文件编写，请参考下一篇博客内容[安卓集成发布详解（二）](),主要包括签名文件处理及多渠道版本编译知识
