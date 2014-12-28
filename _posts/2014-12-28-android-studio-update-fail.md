---
layout: post
title: Android Studio 自动更新失败解决办法
---
更新了最新的AndroidStudio之后，每次点击IDE中的check update之后，总是提示说
```
Connection failed. Please check your network connection and try again
```,然后google搜索解决办法，最后搜索到以下解决办法，故记录之。

在软件安装目录的**bin**文件夹下找到**.vmoptions**文件，打开编辑加上以下内容。

{% highlight ruby %}
-Djava.net.preferIPv4Stack=true
-Didea.updates.url=http://dl.google.com/android/studio/patches/updates.xml
-Didea.patches.url=http://dl.google.com/android/studio/patches/
{% endhighlight %}