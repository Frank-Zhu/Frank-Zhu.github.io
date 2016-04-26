---
layout: post
title: Android项目基础规范
tags: [android]
categories: [android]
---

# Android项目基础规范

#### 代码管理

1. Git 使用 rebase 命令来合并分支，尽量不要直接 merge 导致分支代码日志混乱。
2. 开发新功能时，自己在本地创建 feature 分支开发，功能开发完毕之后，参照上面合并流程操作功能的合并，并删除本地分支，注意不要将本地分支推送到服务器。
3. 平时开发只在 develop 分支和自己的本地分支操作，不要操作 master 和 release 分支，这两个分支只有在功能稳定上线的时候才进行合并操作。当线上版本出现紧急 bug 时，直接基于 master 分支拉取 hotfix 分支，解决 bug 之后再合并到其它各个分支。具体流程可以参见 [Git Flow 流程](http://nvie.com/posts/a-successful-git-branching-model/)
4. git 提交日志尽量将注释缩减为一句话，不要包含详细的内容。
   - Added ( 新加入的需求 )
   - Fixed ( 修复 bug 标注禅道的bug id 或者 bugtags 的 id)
   - Changed ( 完成的任务 )
   - Updated ( 完成的任务，或者由于第三方模块变化而做的变化 )

#### 代码编写规范

##### 编译配置相关

1. 所有用到的第三方库文件统一在项目根目录下的 dependecies.gradle 文件中管理，并在 thirdDependencies 标签中添加引用并注释，需要填写代码库地址，方便后续查看使用及更新。
2. 项目编译用到的 sdk 版本及 app 版本信息统一在项目根目录下的gradle.properties 中管理，其它引用的第三方库工程代码也需要同步更新。
3. 项目中用到的第三方 app id 等信息需要在项目的 build.gradle 中定义对应字段在项目中引用，不要直接在代码中写死，容易出错也不利于维护更新。
4. 项目中用到的颜色规范根据 ued 给出的规范统一定义于项目的 colors 中，不要直接在 xml 布局文件中写死，对于只有一两个地方用到的颜色也需要抽取出来单独定义，能够注释一下更好。
5. 公共风格的控件需要抽取相应的 theme 放到 styles 文件中统一使用。不要直接在页面布局文件中写死控件颜色值或者字号大小，需要用统一的风格   theme，并且加上注释。
6. 布局中需要用到固定宽高的统一在 dimens 中定义好，之后在代码中引用。

##### 命名规范

1. 代码中变量的命名遵照驼峰命名法。

2. 布局文件中的控件命名尽量使用控件缩写做前缀，如 TextView 简写为tv_xx，Imageview 简写为 iv_xx。（不强求，有统一的风格即可）

3. 图片资源命名规范如下：

   - 背景类图片(指图片比较大，一般作为 activity 或者 view 背景的图片)，用 bg_ 为前缀，一般为bg\_描述。如果背景是有状态的显示的，命名规则为 bg\_描述\_状态。
   - 按钮类图片（可以用 shape 自己实现的就不要用图片了），用 btn_ 为前缀，一般为btn\_描述，如果按钮有状态显示的，需要添加状态后缀，如 btn\_描述\_normal 、btn\_描述\_focus 、btn\_描述\_enabled 。
   - 选择框等控件使用的图片用 chx_ 为前缀。
   - Dialog 控件使用的图片用 dg_ 为前缀。
   - Menu 用到的图片用 menu_ 为前缀。
   - 自己用 shape 实现的图片效果用 shape_ 为前缀。
   - 其中有状态切换的图片组，在 drawable 文件夹中用 selector_ 为前缀。

4. Layout 文件命名规范如下：

   - 如果布局文件是用于 activity ，则用 activity_ 为前缀。
   - 如果布局文件是用于 fragment ，则用 fragment_ 为前缀。
   - 如果布局文件是用于 ListView GridView RecycleView等 ，则用 item_ 为前缀。
   - 如果布局文件是用于自定义 View ，则用 view_ 为前缀。
   - 如果布局文件有多处引用使用 ，需要抽取公共引用的布局文件，则用 include_ 为前缀。

5. 字符串资源命名规范如下：

   有一个规则就是如果字符串可能有多处地方需要使用时，尽量用通用的英文单词含义就可以了（如 ok cancel back 等），对于某些字符串资源只有某些特定的范围使用时准照下面的规则。

   - 用于 toast 弹出展示的资源用 toast_ 为前缀。
   - 用于 dialog 展示的资源用 dialog_ 为前缀。
   - 用于页面上标签展示的资源用 label_ 为前缀。

##### 代码编写

1. 页面布局中对于需要填充字符串或者图片查看控件效果的，都用 tools 命名空间进行资源的填充，对于需要隐藏的空间也需要用 tools 命名空间把控件进行显示，这样方便调整 ui ，一句话就是，要善于使用 tools 命名空间来编写布局文件。
2. 对于布局有间距需求的，统一在 root view 级别做统一的上下左右间隔，尽量不要在子控件去做整体布局的上下左右间距处理。
3. 代码中涉及到字符串资源的全部需要提取出来放入 strings.xml 文件中，不要在代码中直接将文本写死，很难维护。
4. 网络请求处理尽量放到 appnetworklibrary 模块下处理，包括 model 及 Rxjava 处理数据之后返回也放到这个模块下。
5. 项目中用到 EventBus 事件的，事件消息都放到主项目下的 event 包下，不要直接定义字符串事件处理，如果需要发送字符串事件处理的，改用发送本地广播来处理，通过 LocalBroadcastManager 来发送。
6. 页面涉及 ViewPager 的，可以使用 FZBaseViewPagerAdapter 来设置适配器，其中的 Fragment 需要继承 FZBaseViewPagerFragment 来实现懒加载，不要一次把全部页面的数据都加载出来，切记！！！
7. RecycleView 的 view holder 需要继承 FZBaseViewHolder 来实现，在绑定数据的时候直接调用 bindViewData 即可。 
8. 其它相应的工具类在 appbaselibrary 模块中，有些有包含的可以直接使用，没有包含的如果是可以通用的直接在模块中添加即可，否则在主项目中添加。
9. 数据模型中，如果没有需求需要页面级别传递数据的话，不需要实现数据的序列化（Parcelable ／ Serializable）。
10. 其它欢迎补充。


-------

附上基础库代码链接[FZBaseLib](https://github.com/Frank-Zhu/FZBaseLib)





