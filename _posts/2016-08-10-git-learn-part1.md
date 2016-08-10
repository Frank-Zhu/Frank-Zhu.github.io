—
layout: post
title: Git进阶之路（一）
tags: [git]
categories: [tools]
---


### 准备工作

---

#### Git客户端下载

[SourceTree 推荐](https://www.sourcetreeapp.com/)

[TortoiseGit 小乌龟](https://tortoisegit.org/)

#### Git免费仓库

[Bitbucket 支持开源仓库及私人仓库](https://bitbucket.org/)  完全免费且没有仓库限制，团队的话有团队人数限制，关键是私人仓库免费啊，如果私人项目比较多的话推荐用这个。

[GitHub 支持开源仓库及私人仓库(私人仓库收费)](https://github.com/) 如果你开源项目比较多，建议选择这个，私人仓库的话是需要收取费用的，不过一个月也才几美元，一般赚名声的话推荐用这个。

#### 比较工具(用于解决冲突)

[BeyondCompare 推荐](http://www.scootersoftware.com/download.php)

[Workshare Compare](http://www.workshare.com/products/compare-documents/)

[UltraCompare Pro](http://www.ultraedit.cn/products/ultracompare/features/compare_files.html)

### 基础介绍

---
### 1. Git 与 SVN 区别点

- GIT 是分布式的，SVN 不是。这是 GIT 和其它非分布式的版本控制系统，例如 SVN，CVS 等，最核心的区别。
- GIT 把内容按元数据方式存储，而 SVN 是按文件：所有的资源控制系统都是把文件的元信息隐藏在一个类似 .svn，.cvs等的文件夹里。
- GIT 分支和 SVN 的分支不同：分支在 SVN 中一点不特别，就是版本库中的另外的一个目录。
- GIT 没有一个全局的版本号，而 SVN 有, 目前为止这是跟 SVN 相比 GIT 缺少的最大的一个特征。
- GIT 的内容完整性要优于 SVN 。GIT 的内容存储使用的是 SHA-1 哈希算法，这能确保代码内容的完整性，确保在遇到磁盘故障和网络问题时降低对版本库的破坏。
- Git 只适用于文本内容管理，对于二进制文件无法直接查看修改的内容对比，不太适合用于管理二进制文件内容。

### 2. 工作区（Working Directory）和版本库（Repository）

​	
![工作区和版本库示意图](http://www.worldhello.net/wpfiles/2010/11/git-stage.png)

**说明：**

- 工作区就是创建仓库的文件夹如（ learngit 文件夹就是一个工作区）
- 版本库就是工作区的隐藏目录`.git`,版本库中有暂存区（ stage / index ）和分支（ master ）
- git add 实际是把文件添加到暂存区， git commit 把暂存区的内容提交到当前分支



好了，上面一些官方介绍完毕（虾扯淡），下面开始进入正题（基于 IDE 傻瓜式操作，没有命令行教学）。



### 基本使用

---

1. 创建版本库 **`git init`**

   * 创建 本地/服务器 git 仓库文件夹，名为：**`learngit` **

     ![创建本地仓库示意图01](https://bytebucket.org/Frank-Zhu/learngit/raw/bfbcad16234df9d8f2c8069cd9e7c1d9f1aef051/01.png?token=5fb51403c519f77706e4e90c54896f1e5ab79755)

     ![创建服务器仓库示意图02](https://bytebucket.org/Frank-Zhu/learngit/raw/bfbcad16234df9d8f2c8069cd9e7c1d9f1aef051/02.png?token=0f9a9ef907c94d7abb695504b3eb4a1877c0b4a4)

2. 添加文件 **`git add 文件全名包括后缀`** 

   - 将文件添加到暂存区，已添加到暂存区的文件会在`已暂存文件`区域显示，且文件会变成绿色，未暂存的文件会在 `未暂存的文件` 区域显示，如果要把未暂存的文件添加到已暂存文件直接点下前面的勾选框即可。

     ![添加文件示意图03](https://bytebucket.org/Frank-Zhu/learngit/raw/0b1b2887b5a981dff60dc0f34ccc4a57a9678947/03.png?token=6b8a79d8c3b42e121812bdb4621a10793e220cce)

3. 提交文件 **`git commit -m "commit message"`**

   - 将已缓存的文件提交到本地仓库并填写本次提交信息log

     ![提交文件示意图04](https://bytebucket.org/Frank-Zhu/learngit/raw/0b1b2887b5a981dff60dc0f34ccc4a57a9678947/04.png?token=42102db1cd151923373b2293dbde9ed681a9446a)

4. 关联远程仓库 **`git remote add origin xxx.git`**

   - 将本地仓库于远程服务器仓库关联

     ![关联远程仓库示意图05](https://bytebucket.org/Frank-Zhu/learngit/raw/bfbcad16234df9d8f2c8069cd9e7c1d9f1aef051/05.png?token=e35ed21f35d5f579ae57c5b1ba18e8aad9b27982)

     ![关联远程仓库示意图06](https://bytebucket.org/Frank-Zhu/learngit/raw/bfbcad16234df9d8f2c8069cd9e7c1d9f1aef051/06.png?token=3d6a04a8bbf58d8862a9e96ad0c5dcfe17e211c3)

5. 推送文件  **`git push -u origin master`** 

   - 将本地的修改提交推送到远程服务器仓库

     ![本地提交推送到服务器示意图07](https://bytebucket.org/Frank-Zhu/learngit/raw/bfbcad16234df9d8f2c8069cd9e7c1d9f1aef051/07.png?token=03d789ff0a41c037c1350ca6ca2de5439b5144c0)

6. 拉取服务器提交 **`git pull origin master`**

   - 将服务器上别人的提交拉取到本地

     ![拉取服务器提交示意图08](https://bytebucket.org/Frank-Zhu/learngit/raw/0115b91523ba71f0f12f44eaa14ca144ed457153/08.png)


### 进阶

---

1. 分支管理

   * 分支创建 **`git branch 分支名`** 

     ![分支创建示意图09](https://bytebucket.org/Frank-Zhu/learngit/raw/0115b91523ba71f0f12f44eaa14ca144ed457153/09.png)

   * 分支切换 **`git checkout 分支名`**

     双击某一个分支即可进行分支切换

   * 分支查看 **`git branch`**

     选中某个分支可以看到分支所有记录

     ![分支查看示意图10](https://bytebucket.org/Frank-Zhu/learngit/raw/0115b91523ba71f0f12f44eaa14ca144ed457153/10.png)

   * 分支提交 

     分支上的代码提交和上面的一样处理即可，没什么区别。分支的推送也很简单，和代码的推送类似

     ![分支推送示意图11](https://bytebucket.org/Frank-Zhu/learngit/raw/0115b91523ba71f0f12f44eaa14ca144ed457153/11.png)

   * 分支合并 **`git merge 分支名`**

     当我们分支上的功能开发完毕之后，需要将分支的内容合并到主分支，这个时候可以这么处理

     ![分支合并示意图12](https://bytebucket.org/Frank-Zhu/learngit/raw/0115b91523ba71f0f12f44eaa14ca144ed457153/12.png)

   * 分支删除 **`git branch -d 分支名`**

     当分支合并完之后，分支就可以删除了，只需要切换到别的分支，然后选中要删除的分支右键，选择删除分支即可，弹出框中有 **`强制删除`** 选项，对应的命令是 **`git branch -D 分支名`** 当分支无法删除时可以选择强制删除选项删除即可。

     ![删除分支示意图13](https://bytebucket.org/Frank-Zhu/learngit/raw/0115b91523ba71f0f12f44eaa14ca144ed457153/13.png)

2. 合并冲突解决

   当多人协作工作时，可能会有更改到相同文件的情况，这样就有可能会有冲突需要处理。当然，我们应该在工作分配时尽量避免多人负责一个模块的情况来避免不必要的代码冲突，当冲突不可避免时，我们就需要来处理冲突，这个时候就要祭出  **`BeyondCompare`** 这个合并神器。我们右键选中需要解决冲突的文件，弹出的菜单中选择其它合并工具即可。

   ![合并冲突示意图14](https://bytebucket.org/Frank-Zhu/learngit/raw/0115b91523ba71f0f12f44eaa14ca144ed457153/14.png)

   ![合并冲突示意图15](https://bytebucket.org/Frank-Zhu/learngit/raw/0115b91523ba71f0f12f44eaa14ca144ed457153/15.png)

3. 后悔药 **`git reset HEAD`** **`get reset --hard 提交记录`** **`git revert 提交记录`**

   - 未提交

     在代码未提交时想丢弃本次修改，重新开始开发时可能会用到这个。这种也是最简单的，直接选中需要撤销的文件，右键选择重置菜单即可。

     ![文件撤销示意图16](https://bytebucket.org/Frank-Zhu/learngit/raw/0115b91523ba71f0f12f44eaa14ca144ed457153/16.png)

   - 已提交

     当代码已提交到本地分支，但是还没有推送到服务器仓库时，想回退掉提交，这个时候就可以使用 **`get reset --hard 提交记录`** 命令来回退提交。

     ![已提交记录重置示意图17](https://bytebucket.org/Frank-Zhu/learngit/raw/0115b91523ba71f0f12f44eaa14ca144ed457153/17.png)

     ![重置完成示意图18](https://bytebucket.org/Frank-Zhu/learngit/raw/0115b91523ba71f0f12f44eaa14ca144ed457153/18.png)

   - 已推送

     有时候我们可能脑抽，不小心把错误代码提交了，不止是提交到了本地，还手残的给点了推送同步到了服务器，这个时候事情就大了，我们需要赶紧把这条记录给回退掉，这个时候，我们选中我们需要回滚的的记录，右键点击 **`提交回滚`** 菜单，然后弹窗窗口点击确定，这个时候，我们可以看到代码被回滚了，但是git上有了一条记录就是 **`Revert xxxxx`** 还需要推送到服务器，这个就是有后遗症的，别人是可以看到你的回滚记录的，所以，下次提交的时候一定要注意，要仔细检查好。

     ![回滚提交示意图19](https://bytebucket.org/Frank-Zhu/learngit/raw/0115b91523ba71f0f12f44eaa14ca144ed457153/19.png)

4. 合并补丁 patch

   有时候我们在开发分支功能的时候，有些紧急 bug 是在分支代码上处理的，在处理完毕之后，需要将修改合并到主线和其它开发分支上，这个时候，我们就需要用到 patch 来做代码合并了。

   - 生成补丁 **`git diff`**  **`git format-patch `**

     生成补丁可以使用上面两种命令行形式生成，但是我们有 IDE 啊，当然要无脑操作嘛。生成也很简单，首先选择任意一条记录，点击右键 **`创建补丁`** 菜单，然后在弹出的菜单中选择我们需要生成 patch 的提交记录，注意这里只能选择连续的几条记录来生成 patch ，如果我是要不连续的几条记录怎么办呢？生成多个 patch 就可以了。

     ![生成补丁示意图20](https://bytebucket.org/Frank-Zhu/learngit/raw/0115b91523ba71f0f12f44eaa14ca144ed457153/20.png)

   - 合并补丁 **`git apply 补丁文件`** **`git am 补丁文件`**

     补丁合并用上面两个命令即可，这里要注意一下区别，用 **`am`** 命令时合并补丁时是有保持原来的提交日志的，而用 **`apply`** 则是没有原来提交日志的，这里一般建议是直接用 **`am`**  命令来处理补丁合并。

     ![合并补丁示意图21](https://bytebucket.org/Frank-Zhu/learngit/raw/0115b91523ba71f0f12f44eaa14ca144ed457153/21.png)

     ![合并补丁示意图22](https://bytebucket.org/Frank-Zhu/learngit/raw/0115b91523ba71f0f12f44eaa14ca144ed457153/22.png)

5. 贮藏

   这个比较简单了，不过应用的场景还是挺多的，比如，我们在开发功能的时候，代码还没开发完不能提交，但是又有一个很紧急的 bug 需要修复，这个时候我们就需要把我们未开发完的代码先贮藏起来，保证不影响紧急 bug 的修复，等修复完了，然后再应用贮藏的内容继续完成功能的开发。

   ![贮藏示意图23](https://bytebucket.org/Frank-Zhu/learngit/raw/0115b91523ba71f0f12f44eaa14ca144ed457153/23.png)

   ![恢复贮藏文件示意图](https://bytebucket.org/Frank-Zhu/learngit/raw/0115b91523ba71f0f12f44eaa14ca144ed457153/24.png)

### 高阶玩法(后续填坑)

---

1. 分支衍合
2. git 工作流
3. 标签管理
4. 拉取请求
5. 日志规则
6. 文件忽略



