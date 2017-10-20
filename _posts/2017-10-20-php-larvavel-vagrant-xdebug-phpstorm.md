---
layout: post_default
title: Vagrant+Homestead+xDebug+PhpStorm配置Laravel开发环境
tags: [php]
categories: [laravel5]
---

#### 一、Vagrant安装
去[官网](https://www.vagrantup.com/downloads.html) 下载安装对应版本即可，一路傻瓜式安装。

#### 二、VirtualBox安装

去[官网](https://www.virtualbox.org/wiki/Downloads) 下载安装对应版本即可，一路傻瓜式安装。

#### 三、安装 Homestead Vagrant Box

当安装完 VirtualBox  以及 Vagrant 后，推荐直接安装 [laravel/homestead](https://app.vagrantup.com/laravel/boxes/homestead) Box，此 Box 已经包含了 Laravel 开发的常用软件，包括配置也已经帮忙处理好了。具体的安装和配置可以参考这里 [Laravel 虚拟开发环境 Homestead](https://d.laravel-china.org/docs/5.5/homestead#accessing-homestead-globally)

#### 四、本地安装Vagrant Box

国内由于众所周知的网络原因导致直接安装速度很慢甚至无法安装，下面介绍本地添加安装的方法。

通过别的方式下载到最新的laravel/homestead box包。

##### 第一种方式

1. 通过命令添加本地的 Box 包到 Vagrant。

   {% highlight ruby %}
   vagrant box add laravel/homestead virtualbox.box  #添加本地box virtualbox.box为你本地box的文件路径
   {% endhighlight %}

2. 通过上面的命令添加 Box 之后，如果我们直接运行 vagrant up 命令，会发现还是会去下载 box ，所以我们需要再配置一下默认的 box。我们通过查看 box 列表会发现我们添加的 **laravel/homestead** 没有正确的版本号。

   {% highlight ruby %}
   vagrant box list #查看本机已安装box列表
   laravel/homestead (virtualbox, 0) #会发现版本为0
   {% endhighlight %}

3. 在上面 **三、安装 Homestead Vagrant Box** 步骤中会下载一个 **[Homestead](https://github.com/laravel/homestead)** 项目，我们可以直接修改目录下的 **Vagrantfile** 文件，加入下面的配置即可

   {% highlight ruby %}
   config.vm.box = "laravel/homestead" #box的名字（需与盒子列表中的一致）
   config.vm.box_version = "4.0"       #box的版本号（需与盒子列表中的一致）
   config.vm.box_check_update = false  #box是否检查更新
   {% endhighlight %}

4. 或者直接修改 这个目录下的 **~/.vagrant.d/boxes/laravel-VAGRANTSLASH-homestead/0** 的0为你当前box的版本号即可，即 **~/.vagrant.d/boxes/laravel-VAGRANTSLASH-homestead/4.0** ，并在这个目录添加一个**metadata_url** 文件，文件内容如下

   {% highlight ruby %}
   https://atlas.hashicorp.com/laravel/homestead
   {% endhighlight %}

5. 执行 **vagrant up** 或者 **homestead up** 即可

##### 第二种方式

1. 通过创建一个 **metadata.json** 文件的形式添加 box ，文件内容如下

   {% highlight ruby %}
   {
     "name": "laravel/homestead", //box的名字
     "versions": [
       {
         "version": "4.0", //box的版本号
         "providers": [
           {
             "name": "virtualbox",
             "url": "virtualbox.box" //本地box的文件路径
           }
         ]
       }
     ]
   }
   {% endhighlight %}

2. 之后执行下面的命令来添加 box

   {% highlight ruby %}
   vagrant box add metadata.json
   {% endhighlight %}

3. 执行 **vagrant up** 或者 **homestead up** 即可

#### 五、配置PhpStorm

##### Vagrant插件配置

1. 先下载 PhpStorm 的 Vagrant 插件，然后打开 **Preferences -> Vagrant** ，其中的 **Vagrant executable** 选项填写 vagrant 的安装目录 **/opt/vagrant/bin/vagrant** ，Instance folder 选项填写 **三、安装 Homestead Vagrant Box** 步骤中下载的 **[Homestead](https://github.com/laravel/homestead)** 项目目录 比如 **/Users/xxx/Homestead**

   具体可以参考下图

   ![](https://github.com/Frank-Zhu/Frank-Zhu.github.io/blob/master/img/vagrant/1.png?raw=true)

2. 打开 **Preferences -> Languages&Frameworks -> PHP**，首先选择自己支持的 PHP 版本，之后在 **CLI Interpreter** 选项选择后面的 **…** ，在弹出的菜单中选择 **+** 号添加服务器配置，选择第一项 **From Docker,Vagrant** 那项，弹出的菜单如下图所示，直接点击确认即可，具体可以参考下图

   ![](https://github.com/Frank-Zhu/Frank-Zhu.github.io/blob/master/img/vagrant/2.png?raw=true)

   ![](https://github.com/Frank-Zhu/Frank-Zhu.github.io/blob/master/img/vagrant/3.png?raw=true)

3. 打开 **Preferences -> Languages&Frameworks -> PHP -> Servers** 给自己的项目起一个名字，填写项目的host 地址，**Debugger** 选项选择 **Xdebug** 然后在 **Project files** 项目目录填写项目在 Vagrant 上的绝对目录，就是在 **[Homestead](https://github.com/laravel/homestead)** 项目下 **Homestead.yaml** 文件下配置的项目目录 **sites** 下的项目目录，注意是项目目录，**不是 public 目录**，具体可以参考下图

   ![](https://github.com/Frank-Zhu/Frank-Zhu.github.io/blob/master/img/vagrant/4.png?raw=true)

##### PHP Web Application配置

1. 打开 **Run -> Edit Configurations** ，选择 **+** 号添加 **PHP Web Application** ，然后填写项目名称，**Server** 选择上面步骤添加的服务器即可，具体可以参考下图

   ![](https://github.com/Frank-Zhu/Frank-Zhu.github.io/blob/master/img/vagrant/5.png?raw=true)

2. 上面的配置全部配置好之后，就可以 **Run→Run ProjectName** or **Run→Debug ProjectName** 直接运行项目，愉快的玩耍吧😄😄😄

