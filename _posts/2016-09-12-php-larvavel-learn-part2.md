---
layout: post
title: Php Laravel5 学习笔记(二)
tags: [php]
categories: [laravel5]
---

[视频教程网址](https://laravist.com/series/laravel-5-basic)

#### 数据库操作
make:migration 可以生成数据库迁移文件
Eloquent 可以直接命令行操作数据库数据

#### 数据库字段处理
Model中 **$dates** 属性可以将字段转换为 Carbon 对象
setXXXAttribute 方法可以对参数做预处理  XXX为字段名
scopeXXX 方法可以对查询做统一处理 XXX方法名
Carbon 对象 的 diffForHumans() 可以输出格式化时间

#### 表单验证
Request 对象请求可以自定义规则做表单验证，其中 **authorize** 方法用于身份验证，**rules** 方法用于规则定义
也可以直接在方法中用 **validate** 关键字处理

```$this->validate($request,['title'=>'required','content'=>'required']);```


#### 数据绑定及路由注册
**Route::resource** 可以自动注册 Controller 所有的路由

```Route::resource('articles','ArticlesController');```

**Form::model** 可以绑定数据结构，自动填充内容显示
**include** 标签引入布局文件，默认路径是 **views** 目录 

#### 用户登录注册
偷懒写法，直接用** make:auth** 命令生成即可