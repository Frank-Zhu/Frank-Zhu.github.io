---
layout: post
title: Android编译环境配置(非公开)
tags: [build]
categories: [android]
---

##第一步当然是安装工具及配置环境

###代码管理工具安装
1.代码拉取工具可以到以下地址安装[SourceTree](https://www.sourcetreeapp.com/)

2.下载之后默认安装就可以了，可能需要免费注册一下，不然会有使用期限限制

3.在安装的时候你可能还需要[GIT](https://git-scm.com/downloads)工具,下载安装一下即可

###JAVA环境安装
1.[JDK1.8下载地址64位](thunder://QUFodHRwOi8vZG93bmxvYWQub3JhY2xlLmNvbS9vdG4tcHViL2phdmEvamRrLzh1NjUtYjE3L2pkay04dTY1LXdpbmRvd3MteDY0LmV4ZVpa/)，你也可以在[这里](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下载你对应的系统版本

2.下载之后安装好JDK到你的电脑，假如路径是这个**C:\Program Files\Java\jdk1.7.0_51**这个路径可能不一样，下面的配置换成你自己的就可以

3.然后右键计算机->属性->高级系统设置 在弹出的菜单中切换到高级页面，点击环境变量,在弹出的菜单中点击底部系统变量一栏的新建按钮，在变量名一栏中填写**JAVA_HOME**,在变量值一栏中填写你上面的安装路径，比如我的就是**C:\Program Files\Java\jdk1.7.0_51**填完之后点击确定，然后在系统变量一栏找到一个叫**Path**的变量名，双击打开编辑，把下面这行加载变量值的最后**;%JAVA_JDK_HOME%\bin;** 然后点确定保存一下，注意前后的**;号**注意前后的**;号**注意前后的**;号**重要的事情说三遍

4.验证一下你的环境是不是配置好了，首先启动命令行，输入以下命令**java -version**，看是否有如下信息输出,如果能看到正确的版本信息，那就说你的环境配置没有问题
```java
C:\Users\rongyi>java -version

java version "1.7.0_51"
Java(TM) SE Runtime Environment (build 1.7.0_51-b13)
Java HotSpot(TM) 64-Bit Server VM (build 24.51-b03, mixed mode)
```

###Android SDK 环境安装
1.这个文件有点大，我单独通过讨论组发给你们，如果没有保存接收的，可以直接找我要一下即可。当然，如果你比较喜欢折腾，那你可以自己安装一下步骤如下

1)先去官网下载一下SDK安装器，傻瓜式的，下载好之后点击安装即可

2)安装好之后，到安装目录下的tools目录，点击android.bat脚本启动Android SDK Manager工具，由于众所周知的原因，这个更新在国内很慢，所以你需要设置一下腾讯的开源代理，步骤如下：
>1.启动 Android SDK Manager ，打开主界面，依次选择『Tools』、『Options...』，弹出『Android SDK Manager - Settings』窗口；

>2.在『Android SDK Manager - Settings』窗口中，在『HTTP Proxy Server』和『HTTP Proxy Port』输入框内填入 android-mirror.bugly.qq.com 镜像服务器地址(不包含http://，如下图)和端口8080，并且选中『Force https://... sources to be fetched using http://...』复选框。设置完成后单击『Close』按钮关闭『Android SDK Manager - Settings』窗口返回到主界面；

>依次选择『Packages』、『Reload』。

3)然后你就可以更新最新的tools和buildtools工具了，还需要更新最新的SDK以及支持库文件

2.安装好之后就是配置环境了,假设你的安装或者解压之后的路径是这个，**D:\Android\sdk**，和设置Java环境变量一样

3.右键计算机->属性->高级系统设置 在弹出的菜单中切换到高级页面，点击环境变量,在弹出的菜单中点击底部系统变量一栏的新建按钮，在变量名一栏中填写**ANDROID_HOME**,在变量值一栏中填写你上面的安装路径，比如我的就是**D:\Android\sdk**填完之后点击确定，然后在系统变量一栏找到一个叫**Path**的变量名，双击打开编辑，把下面这行加载变量值的最后**;%ANDROID_HOME%\tools;%ANDROID_HOME%\platform-tools;** 然后点确定保存一下，注意前后的**;号**注意前后的**;号**注意前后的**;号**重要的事情说三遍

4.验证一下你的环境是不是配置好了，首先启动命令行，输入以下命令**adb version**，看是否有如下信息输出,如果能看到正确的版本信息，那就说你的环境配置没有问题
```java
C:\Users\rongyi>adb version

Android Debug Bridge version 1.0.29
```

###Gradle环境配置
1.[Gradle下载](thunder://QUFodHRwczovL3NlcnZpY2VzLmdyYWRsZS5vcmcvZGlzdHJpYnV0aW9ucy9ncmFkbGUtMi44LWFsbC56aXBaWg==/)也可以在[官网下载](http://gradle.org/gradle-download/)

2.下载解压到一个路径地址即可，不需要安装

3.然后就是环境配置，和上面JAVA和Android SDK配置是一样，这一次是新建一个**GRADLE_HOME**变量，然后在**Path**添加**;%GRADLE_HOME%\bin;** 

4.验证一下环境变量配置

```java
C:\Users\rongyi>gradle -v
Gradle 2.2.1

Build time:   2014-11-24 09:45:35 UTC
Build number: none
Revision:     6fcb59c06f43a4e6b1bcb401f7686a8601a1fb4a

Groovy:       2.3.6
Ant:          Apache Ant(TM) version 1.9.3 compiled on >December 23 2013
JVM:          1.7.0_51 (Oracle Corporation 24.51-b03)
OS:           Windows 7 6.1 amd64
```

##环境搞好了第二步当然是拉取对应分支的代码

1.代码地址[MallLife](https://bitbucket.org/rongyiwang/malllife)
[MallShop](https://bitbucket.org/rongyiwang/cmssellers)

2.点击Clone,会启动SorceTree开始Clone代码，选择一个目录，然后等待代码拉取完成即可

3.代码拉取完成之后，一般都是在master分支，需要再checkout Develop分支和Release分支下来
>分支含义 Develop分支为当前开发的最新分支，为最新的代码，变更也最频繁

>Release分支为线上最稳定版本，一般只有版本上线的时候会做代码合并和更新

4.知道分支了之后，就是编译版本了，下面进入正题，等等，难道我上面说的都是废话，好奇怪

1)打开命令行，切换到当前代码目录，比如我的**D:\Android\Code\MallLife>**

2)然后执行一下命令 **gradle clean**清除一下编译信息,看到下面的信息就算成功了

```java
D:\Android\Code\MallLife>gradle clean
:ActionItemBadgeLlibrary:clean
:AndroidTreeView:clean
:app:clean
:multi-image-selector:clean
BUILD SUCCESSFUL
Total time: 20.109 secs
```

3)然后执行**gradle a_216R**,之后就等着吧，这个时间有点长，等看到下面的信息之后，就编译成功了。

```java
:app:shrink_216ReleaseMultiDexComponents
:app:create_216ReleaseMainDexClassList
:app:retrace_216ReleaseMainDexClassList
:app:dex_216Release
:app:process_216ReleaseJavaRes UP-TO-DATE
:app:shrink_216ReleaseResources
:app:validateReleaseSigning
:app:package_216Release
:app:zipalign_216Release
:app:assemble_216Release
```

>BUILD SUCCESSFUL

4)解释一下上面的命令行一，首先**a**是简写，**_216**是渠道号，因为gradle渠道不支持数字开头，所以会加上下划线开始，后面的**R**是Release的简写，整个意思拼起来就是编译216环境下的Release包。

5)然后就是最关心的Release包在哪个目录躺着，这个APK文件是在代码目录下的APK目录下，全路径如下：**D:\Android\Code\MallLife\app\build\outputs\apk**主要是那个带版本号和时间的那个包哦～

到此基本就可以结束了，哦，对，你可能还需要一些黑科技，那你接着往下看吧。

##这里是黑科技
有时候你可能需要修改服务器地址或者要不同的渠道包，但是我又太懒没有给你加，那么你就可以自己来添加渠道，不过要记住不要提交代码哦，在本地修改就好了。

好了，下面就告诉你怎么来使用黑魔法，首先在项目目录下的app目录找到一个叫build.gradle的文件，打开它，他大概长这个样子

```java
defaultConfig {
        applicationId "com.rongyi.rongyiguang"
        minSdkVersion Integer.parseInt(MIN_SDK_VERSION)
        targetSdkVersion Integer.parseInt(ANDROID_BUILD_TARGET_SDK_VERSION)
        versionCode Integer.parseInt(VERSION_CODE)
        versionName VERSION_NAME

        // dex突破65535的限制
        multiDexEnabled true
        // 默认是umeng的渠道
        manifestPlaceholders = [UMENG_CHANNEL_VALUE: "umeng"]
    }
    buildTypes {
        debug {
            buildConfigField "boolean", "LOG_DEBUG", "true"//是否输出LOG信息
            buildConfigField "String", "API_HOST", "\"https://192.168.1.206\""//服务器Api Host
            buildConfigField "String", "API_SHARE_HOST", "\"http://192.168.1.206/app\""//分享
            minifyEnabled false
            zipAlignEnabled false
            shrinkResources false
            signingConfig signingConfigs.debug
        }

        release {
            buildConfigField "boolean", "LOG_DEBUG", "true"//是否输出LOG信息
            minifyEnabled true//混淆编译
            zipAlignEnabled true
            //移除无用的资源文件
            shrinkResources true
            signingConfig signingConfigs.release
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_7
        targetCompatibility JavaVersion.VERSION_1_7
    }

    lintOptions {
        disable 'InvalidPackage'
        abortOnError false
    }

//    packagingOptions {
//        exclude 'META-INF/services/javax.annotation.processing.Processor'
//    }

    applicationVariants.all { variant ->
        variant.outputs.each { output ->
            def outputFile = output.outputFile
            if (outputFile != null && outputFile.name.endsWith('.apk')) {
                File outputDirectory = new File(outputFile.parent);
                def fileName
                if (variant.buildType.name == "release") {
                    fileName = "rongyiguang_v${defaultConfig.versionName}_${packTime()}_${variant.productFlavors[0].name}.apk"
                } else {
                    fileName = "rongyiguang_v${defaultConfig.versionName}_${packTime()}_beta.apk"
                }
                output.outputFile = new File(outputDirectory, fileName)
            }
        }
    }

    productFlavors {
        _216 {
            buildConfigField "String", "API_HOST", "\"https://192.168.1.216\""//服务器Api Host
            buildConfigField "String", "API_SHARE_HOST", "\"http://192.168.1.216/app\""//分享
        }
        _206 {
            buildConfigField "String", "API_HOST", "\"https://192.168.1.206\""//服务器Api Host
            buildConfigField "String", "API_SHARE_HOST", "\"http://192.168.1.206/app\""//分享
        }
        _224 {
            buildConfigField "String", "API_HOST", "\"https://192.168.1.224\""//服务器Api Host
            buildConfigField "String", "API_SHARE_HOST", "\"http://192.168.1.224/app\""//分享
        }
        _162 {
            buildConfigField "String", "API_HOST", "\"https://192.168.1.162\""//服务器Api Host
            buildConfigField "String", "API_SHARE_HOST", "\"http://192.168.1.162/app\""//分享
        }
        _186 {
            buildConfigField "String", "API_HOST", "\"https://192.168.1.186\""//服务器Api Host
            buildConfigField "String", "API_SHARE_HOST", "\"http://192.168.1.186/app\""//分享
        }
        _apiv4 {
            buildConfigField "String", "API_HOST", "\"https://api.v4.rongyi.com\""//服务器Api Host
            buildConfigField "String", "API_SHARE_HOST", "\"http://api.v4.rongyi.com/app\""//分享
        }
        _apiv6 {
            buildConfigField "String", "API_HOST", "\"https://app.rongyiguang.com\""//服务器Api Host
            buildConfigField "String", "API_SHARE_HOST", "\"http://app.rongyiguang.com/app\""//分享
        }
        baidu {
        }
        _91zs {
        }
        hiapk {
        }
        _360 {
        }
        yyb {
        }
        anzhi {
        }
        zhuoyi {
        }
        yingyh {
        }
        wdouj {
        }
        sougoushouji {
        }
        sougou {
        }
        huawei {
        }
        xiaomi {
        }
        meizu {
        }
        lenovo {
        }
        ydmm {
        }
    }
    
```

这个只是一部分，你需要关心的东西在这里

```java
_216 {
            buildConfigField "String", "API_HOST", "\"https://192.168.1.216\""//服务器Api Host
            buildConfigField "String", "API_SHARE_HOST", "\"http://192.168.1.216/app\""//分享API地址
        }
```
没错，如果你要添加新的渠道包，就新增一个这个结构就可以了，然后改一下名字，你就可以执行下马这个命令了，比如修改为_225,那么就可以执行**gradle a_225R**命令来打包225环境的包了，是不是很简单，对，其实我说的都是废话，就这样吧。一定要记得改**API_HOST**字段对应的值啊，不然到时候问我说为啥API地址没变，我是不会承认的啊～

好了，终于可以摆脱频繁打包了，以后你们自己打包吧，对了，这个是不是也可以直接给运营的人用啊，好像是的。

最好再啰嗦一句，看了教程之后如果你实在是还搞不定呢，那你就叫我，我帮你配置好.

以上