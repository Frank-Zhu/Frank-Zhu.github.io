---
layout: post
title: 安卓集成发布详解（二）
tags: [gradle]
categories: [android]
---

上一篇主要讲了安卓版本编译版本发布的过程，本篇主要写版本编译脚本的实现，包括签名文件处理及多渠道版本编译。[安卓集成发布详解（一）](http://frank-zhu.github.io/android/2015/06/14/android-release_app_version/)

##一、签名部分编写
gradle本身支持直接签名，只需要在releas部分添加如下代码即可

{% highlight ruby %}
	signingConfigs {
        debug {

        }
        release {
            storeFile file("../yourapp.keystore")
    		storePassword "your password"
    		keyAlias "your alias"
    		keyPassword "your password"
        }
    }
	
	buildTypes {
        debug {
            minifyEnabled false
            zipAlignEnabled false
            shrinkResources false
            signingConfig signingConfigs.debug
        }

        release {
            minifyEnabled true//混淆编译
            zipAlignEnabled true
            //移除无用的资源文件
            shrinkResources true
            signingConfig signingConfigs.release
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
    
{% endhighlight %}

一般填上上面的代码即可执行签名，但是一般这种方式不太安全，一般建议不要在build.gradle文件中写上签名文件的密码，这是由于build.gradle文件一般都会集成到代码的版本控制中，这样所有人都会有签名文件的密码，会有后续的麻烦。所以呢，我们接下来要做的就是把签名文件的密码隔离起来，写到一个配置文件中，此配置文件不包含在代码版本控制中，这样其他开发者就不会知道签名文件的密码。

gradle的配置文件一般以.properties结束，我们先新建一个signing.properties文件，内容如下：

{% highlight ruby %}

STORE_FILE=yourapp.keystore
STORE_PASSWORD=your password
KEY_ALIAS=your alias
KEY_PASSWORD=your password

{% endhighlight %}

**注意没有字符串双引号""**

**注意没有字符串双引号""**

**注意没有字符串双引号""**

重要的事情说三遍

我们上面新建了一个配置文件，用来保存签名文件的信息，并且此文件不会加入到代码版本控制中，达到保护签名文件的目的。接下来我们就应该在guild.gradle文件中读取我们的配置文件，读取的代码如下：

{% highlight ruby %}

File propFile = file('signing.properties');
if (propFile.exists()) {
    def Properties props = new Properties()
    props.load(new FileInputStream(propFile))
    if (props.containsKey('STORE_FILE') && props.containsKey('STORE_PASSWORD') &&
            props.containsKey('KEY_ALIAS') && props.containsKey('KEY_PASSWORD')) {
        android.signingConfigs.release.storeFile = file(props['STORE_FILE'])
        android.signingConfigs.release.storePassword = props['STORE_PASSWORD']
        android.signingConfigs.release.keyAlias = props['KEY_ALIAS']
        android.signingConfigs.release.keyPassword = props['KEY_PASSWORD']
    } else {
        android.buildTypes.release.signingConfig = null
    }
} else {
    android.buildTypes.release.signingConfig = null
}

{% endhighlight %}

代码很简单，就是读取文件，然后拿到签名需要的四个变量值分别赋值即可。

以上就是签名部分代码，是不是很简单😊

##二、多渠道打包
gradle的多渠道打包很简单，因为gradle已经帮我们做好了很多基础功能。下面以友盟统计为例说明（貌似只要是讲多渠道打包都离不开友盟这货😊）

一般友盟统计在AndroidManifest.xml里面会有这么一段声明
{% highlight ruby %}

<meta-data
    android:name="UMENG_CHANNEL"
    android:value="CHANNEL_ID" />

{% endhighlight %}
其中CHANNEL_ID就是友盟的渠道标示，多渠道的实现一般就是通过修改这货的值来实现的,接下来将一步一步来实现多渠道版本打包。

1)在AndroidManifest.xml里配置PlaceHolder,用与在build.gradle文件中来替换成自己想要设置的值

{% highlight ruby %}

<meta-data
    android:name="UMENG_CHANNEL"
    android:value="${UMENG_CHANNEL_VALUE}" />

{% endhighlight %}

2)在build.gradle设置productFlavors，修改PlaceHolder的值

{% highlight ruby %}

productFlavors {
        playStore {
            manifestPlaceholders = [UMENG_CHANNEL_VALUE: "playStore"]
        }
        miui {
            manifestPlaceholders = [UMENG_CHANNEL_VALUE: "miui"]
        }
        wandoujia {
            manifestPlaceholders = [UMENG_CHANNEL_VALUE: "wandoujia"]
        }
    }

{% endhighlight %}

按照上面两步即可编译打多渠道包了，命令是**gradle assembleRelease**，可以打包所有的多渠道包，通过下面这张图可以看到gradle可以执行的task。
!()[https://github.com/Frank-Zhu/Frank-Zhu.github.io/blob/master/img/android-release_app_build_gradle/1.png]

如果只是想打单渠道包，则执行相应的task即可，如**gradle assemblePalyStoreRelease**就是打PlayStore渠道的Release版本。

3)一般打出来的包我们希望可以带上打包时间，也就是希望可以对最终的文件名做修改，而修改文件名也很简单，参考以下代码即可实现

{% highlight ruby %}

def packageTime() {
    return new Date().format("yyyy-MM-dd", TimeZone.getTimeZone("UTC"))
}

android{

applicationVariants.all { variant ->
        variant.outputs.each { output ->
            def outputFile = output.outputFile
            if (outputFile != null && outputFile.name.endsWith('.apk')) {
                File outputDirectory = new File(outputFile.parent);
                def fileName
                if (variant.buildType.name == "release") {
                    fileName = "app_v${defaultConfig.versionName}_${packageTime()}_${variant.productFlavors[0].name}.apk"
                } else {
                    fileName = "app_v${defaultConfig.versionName}_${packageTime()}_beta.apk"
                }
                output.outputFile = new File(outputDirectory, fileName)
            }
        }
    }

}

{% endhighlight %}



##三、buildConfigField自定义配置


以上
