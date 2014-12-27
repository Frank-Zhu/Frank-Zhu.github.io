---
layout: post
title: 编写Android Proguard File
---

###ProGuard的常用语法
```
-libraryjars class_path 应用的依赖包，如android-support-v4
-keep [,modifier,...] class_specification 不混淆某些类
-keepclassmembers [,modifier,...] class_specification 不混淆类的成员
-keepclasseswithmembers [,modifier,...] class_specification 不混淆类及其成员
-keepnames class_specification 不混淆类及其成员名
-keepclassmembernames class_specification 不混淆类的成员名
-keepclasseswithmembernames class_specification 不混淆类及其成员名
-assumenosideeffects class_specification 假设调用不产生任何影响，在proguard代码优化时会将该调用remove掉。如system.out.println和Log.v等等
-dontwarn [class_filter] 不提示warnning
```
[proguard更多语法](http://proguard.sourceforge.net/index.html#manual/usage.html)

###注意下列类不能进行混淆：
```
    (1)、反射用到的类
    (2)、在AndroidManifest中配置的类(Activity、Service等的子类及Framework类默认不会进行混淆)
    (3)、Jni中调用的类
```

###常用编写
```java
# Obfuscation parameters:
#-dontobfuscate
-useuniqueclassmembernames
-keepattributes SourceFile,LineNumberTable
-allowaccessmodification

#用到反射及注解需要加上下面三个规则
-keepattributes Signature
-keepattributes *Annotation*
-keepattributes EnclosingMethod

# Preserve all native method names and the names of their classes.
-keepclasseswithmembernames class * {
    native <methods>;
}

#不混淆Parcelable的子类，防止android.os.BadParcelableException
-keep class * implements android.os.Parcelable {
  public static final android.os.Parcelable$Creator *;
}

#不混淆Serializable的子类
# Explicitly preserve all serialization members. The Serializable interface
# is only a marker interface, so it wouldn't save them.
-keepclassmembers class * implements java.io.Serializable {
    static final long serialVersionUID;
    private static final java.io.ObjectStreamField[] serialPersistentFields;
    private void writeObject(java.io.ObjectOutputStream);
    private void readObject(java.io.ObjectInputStream);
    java.lang.Object writeReplace();
    java.lang.Object readResolve();
}

```
一般填上上面的即可混淆了，不过大部分代码都会被混淆掉，在编译的时候可能会报错，由于现在大部分都会用到
比较多的开源库，一般开源库会给出混淆的选项，只要添加给出的混淆选项即可。只需要注意两点即可，一就是用到
GSON之类的库的时候需要保证自己的数据实体结构类不被混淆，不然会无法自动解析。二就是如果有用到WEBView的JS调用也需要保证写的接口方法不会混淆，下面给出一些常用库文件的混淆代码规则编写实例。
[GSON Proguard  Example](https://code.google.com/p/google-gson/source/browse/trunk/examples/android-proguard-example/proguard.cfg?r=878)
```
# Keep Actionbarsherlock
-dontwarn com.actionbarsherlock.**
-keepattributes *Annotation*
-keep class android.support.v4.app.** { *; }
-keep interface android.support.v4.app.** { *; }
-keep class com.actionbarsherlock.** { *; }
-keep interface com.actionbarsherlock.** { *; }

# Keep Butterknife stuff
-dontwarn butterknife.internal.**
-keep class **$$ViewInjector { *; }
-keepnames class * { @butterknife.InjectView *;}

#Keep square Retrofit okhttp stuff
-dontwarn com.squareup.okhttp.internal.huc.**
-dontwarn okio.**
-dontwarn com.google.appengine.api.urlfetch.**
-dontwarn rx.**
-keep class com.squareup.** { *; }
-keep class com.viselabs.aquariummanager.util.seneye.** { *; }
-keep class retrofit.http.* { *; }
-keep class retrofit.** { *; }
-keepclasseswithmembers class * {
    @retrofit.** *;
}
-keepclassmembers class * {
    @retrofit.** *;
}

# OkHttp oddities
-dontwarn com.squareup.okhttp.internal.http.*
-dontwarn org.codehaus.mojo.animal_sniffer.IgnoreJRERequirement
-keepnames class com.levelup.http.okhttp.** { *; }
-keepnames interface com.levelup.http.okhttp.** { *; }
-keepnames class com.squareup.okhttp.** { *; }
-keepnames interface com.squareup.okhttp.** { *; }

# Keep the pojos used by GSON or Jackson
-keep class com.futurice.project.models.pojo.** { *; }

# Keep GSON stuff
-keep class sun.misc.Unsafe { *; }
-keep class com.google.gson.** { *; }
# Application classes that will be serialized/deserialized over Gson
-keep class com.xxx.xxx.** { *; }#保持实体数据结构接口不被混淆(也就是被GSON注解的实体结构) 此处xxx.xxx是自己接口的包名

# Keep Jackson stuff
-keep class org.codehaus.** { *; }
-keep class com.fasterxml.jackson.annotation.** { *; }

# Keep Picasso
-keep class com.squareup.picasso.** { *; }
-keepclasseswithmembers class * {
    @com.squareup.picasso.** *;
}
-keepclassmembers class * {
    @com.squareup.picasso.** *;
}

#shareSDK
-dontwarn cn.sharesdk.**
-dontwarn **.R$*
-keep class cn.sharesdk.**{*;}
-keep class com.sina.**{*;}
-keep class **.R$* {*;}
-keep class **.R{*;}
-keep class m.framework.**{*;}

#baidu
-libraryjars libs/baidumapapi_v2_2_0.jar #这个写自己用的的版本JAR
-dontwarn com.baidu.**
-keep class com.baidu.** { *; }
-keep class com.baidu.** { *; }
-keep class vi.com.gdi.bgl.android.**{*;}

#WebView
-keepclassmembers class fqcn.of.javascript.interface.for.webview {
   public *;
}
-keep class com.xxx.xxx.** { *; }#保持WEB接口不被混淆 此处xxx.xxx是自己接口的包名
```
然后就是gradle的设置了
```
    buildTypes {
        release {
            runProguard false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }

    lintOptions {
        disable 'InvalidPackage'
    }

    packagingOptions {
        exclude 'META-INF/services/javax.annotation.processing.Processor'
    }
```

一般用以上的配置就可以了，如果编译的时候还报错误就根据上面的规则一个一个解决就可以。
