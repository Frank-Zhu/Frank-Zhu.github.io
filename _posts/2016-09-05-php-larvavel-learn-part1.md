---
layout: post
title: Php Laravel5 学习笔记(一)
tags: [php]
categories: [laravel5]
---



[视频教程网址](https://laravist.com/series/laravel-5-basic)

### 环境安装

[安装教程](https://laravist.com/article/34)

### 基础命令

`composer create-project laravel/laravel Larvavel5 `

创建工程

`php artisan key:generate`

生成 AppKey

`php -S localhost:8888 -t public`

自己启动服务器

`php artisan serve` 

用内建部分启动服务器

`php artisan make:controller 控制器名 `

可以自动创建Controller文件


### 视图传递变量

视图中用两个大括号输出转义的值**\{\{\$name}\}**,如果不需要转义的话用如下形式**\{!! $name !!\}**
compact 操作符 用法compact(‘first’,’last’),变量名去除**$**符号

### 目录结构

app/Http/routes.php 这个文件下都是一些网站路由，加载一些视图给用户看
app/Http/Controllers 这个目录就是页面的控制器
resources/views 这个目录放的是一些页面的view文件
resources/lang 这个目录是国家化的一些字符串文件
config 这个目录就是一些配置文件，都有注释
.env 是一些本地的配置文件，存放一些敏感信息，文件不会托管到git仓库

### 安装MySQL
到MySQL[官网](http://dev.mysql.com/downloads/mysql/)上下载安装版本，然后点击安装包一步步傻瓜式安装就行。**这里注意一下安装完成之后的弹窗有显示 root 账户的密码，这个最好记录一下**，等会再修改成自己能记住的。
安装完成之后在系统偏好设置的其它选项里面，点击MySQL的启动图标，然后点击**Start MySQL Server**按钮启动MySQL。

### 定义MySQL别名

{% highlight ruby %}



➜  ~ alias mysql=/usr/local/mysql/bin/mysql
➜  ~ alias mysqladmin=/usr/local/mysql/bin/mysqladmin~



{% endhighlight %}



### 设置MySQL账户密码

`mysqladmin -u root -p password 新密码`

这里会要求输入原来的 root 密码，输入完成之后即修改成功。

### 连接MySQL数据库

`mysql -u root -p`

然后按提示输入 root 账户密码就可以连接上数据库了
如果是登陆的远程主机上的数据库则用下面的命令

`mysql -h 主机地址 -u 用户名 -p 用户密码`
