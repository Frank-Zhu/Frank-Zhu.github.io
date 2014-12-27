###安卓APP代码架构搭建
搞安卓开发两年了，一直都想整理一份自己平时开发用得到的代码作为其他项目开发的基础架构，之前也整理过一份，但是碍于自己当时技术的短板，那份代码已经有点落后了，所以今天还是重新整理一份相对合理一点的代码备份下，如果你对之前的版本感兴趣可以点这里--->[AndroidAppCodeFramework](https://github.com/Frank-Zhu/AndroidAppCodeFramework)
* * *
新的代码结构我会采用如下开源库

*   网络通信将使用这些开源库 [Retrofit](https://github.com/square/retrofit)  [OKHTTP](https://github.com/square/okhttp)
*   网络图片加载 [Picasso](https://github.com/square/picasso) / [UIL](https://github.com/nostra13/Android-Universal-Image-Loader)
*   View注解 [Butterknife](https://github.com/JakeWharton/butterknife)
*   DB会用CursorLoader配合contentProvider来做本地缓存
*   JSON解析会采用[GSON](https://code.google.com/p/google-gson/)
*   数据更新通知可能会采用[EventBus](https://github.com/greenrobot/EventBus)或者[otto](https://github.com/square/otto)代替broadcast
*   消息提示[AppMsg](https://github.com/johnkil/Android-AppMsg)代替系统Toast
*   圆形图片[CircleImageView](https://github.com/hdodenhof/CircleImageView)
* * *
![](https://raw.githubusercontent.com/Frank-Zhu/AppCodeArchitecture/master/art/art.jpg)


*  adapter  ListView/GridView 的适配器都放在此文件夹下
*  app  主要放APP的一些常量及配置文件
*  base 主要放activity及fragment的基类文件
*  dao  主要放数据库文件
```
   database  数据库的表
   datahelper  数据库数据操作的帮助类，每一张表派生出一个帮助类操作表
```
*  fragment 主要放不同页面的fragment文件
*  log    APP打LOG的帮助类，可以通过开关关闭LOG的输出，也可以通过设置Gradle的编译混淆方式直接移除函数也可以。
*  model  数据模型类文件夹
*  network  主要放网络操作的文件
```
    callback    HTTP请求的基类文件存放
    controller  主要存放APP中请求网络的控制类操作
```
*  ui    主要放activity文件
*  utils  一些帮助类均放在此文件夹下
*  view  自定义VIEW文件

每一个文件下我都做了注释，引用到的库也给出了链接地址，大部分用法稍微看下WIKI应该都没有什么问题。最后给出代码的下载地址，欢迎 Star 和 Fork [GITHUB CODE 下载](https://github.com/Frank-Zhu/AppCodeArchitecture)

对了，为了编码更快速，可能用上一些IDE的插件会更加快速
1、[ButterKnife Plugin](https://github.com/inmite/android-butterknife-zelezny),一款IDEA插件，这个要配合这个开源库使用[ButterKnife](https://github.com/JakeWharton/butterknife)使用，一个注解库，源至于Dragger,出自大神JakeWharton之手。

2、[Parcelable](https://github.com/mcharmas/android-parcelable-intellij-plugin),安卓数据序列化插件，记得之前不到万不得已是不会用
Parcelable来序列化的，因为他写起来实在是太麻烦了，自从有了一款插件，妈妈再也不用担心我写arcelable序列化数据了，这是咋用咋爽啊

更多的插件工具可以看下我这篇博客---->[Android好用且常用的插件及工具](http://frank-zhu.github.io/2014-08-23-android-tools-and-plugin.html)