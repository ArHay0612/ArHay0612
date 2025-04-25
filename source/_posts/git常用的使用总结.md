---
title: git常用的使用总结
date: 2019-03-16 11:29:32
description: Git是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目
tags:
  - Git
---

### 对一个已存在的远程仓库进行clone和提交代码操作
clone代码
>$ git clone [-b branchName] 远程地址 [filefolder name]

>-b代表clone某个分支,后面跟分支的名称.代码clone到本地后,当前路径会多一个与git 远程项目名相同的文件夹(手工指定文件夹名除外).

>$ cd 文件夹名

提交代码:
>$ git status // 查看当前工作目录的状态

>$ git add . 或 git add -u 或 git add -A(git add --all的缩写) //添加要提交的文件到git暂存区. 相关区别:三条命令对应的git版本不一样也有区别.

>git1.x 版本![](/images/git1.x.jpg)

>git2.x 版本![](/images/git2.x.jpg)

>使用2.x以上版本的git使用-a和.是一样的.本人通常使用.更加方便快捷

>$ git status // 添加文件到暂存区后再次查看确保文件的状态

>$ git commit -m "注释"

>$ git remote -v // 查看当前已经存在的git 远程url

>$ git push -u 远程名称 本地要提交的分支:远程分支 // 远程分支不存在的时候会自动在远程创建该名称分支

### 已有代码在用户本地,远程不存在的情况
>$ cd existing_folder

>$ git init

>$ git status

>$ git remote add origin git@code.aliyun.com:baz/foo.git

>$ git add .

>$ git status

>$ git commit -m "注释"

>$ git push -u origin master:master // 提交本地master到远程master


### 对远程初始化仓库有git history,本地代码也有git history的情况
>针对远程本地都有git 提交的情况,比较特殊.比如阿里云code上面新建项目必须选择对应的模板,会进行对仓库初始化操作.而本地存在之前的项目(已经含有git记录,比如gitlab,coding之类的).推荐方式:先从远程clone下来.然后删除clone下来的文件夹下的文件(.git目录除外).然后commit->push提交到远程

1.删除远程仓库不需要的文件

>$ git clone alicodeurl xxx

>$ cd xxx

>//去资源文件管理器中手动删除除.git目录外的其他文件. linux/osx也可cd 目录再rm删除

>$ git status

>$ git add .

>$ git status

>$ git commit -m "注释"

>$ git push -u origin master:master // 提交本地master到远程master

2.对本地项目进行提交到远程

>$ cd project filefolder

>$ git remote add origin git@code.aliyun.com:baz/foo.git

>$ git pull origin master --allow-unrelated-histories // 会弹出merge的编辑器,删除或者增加内容后退出

>$ git add .

>$ git commit -m "注释"

>$ git push -u origin master:master // 提交本地master到远程master

### 项目开发中的分支使用

切换分支
>$ git checkout 分支名称

创建分支dev

>$ git checkout -b dev  // 创建并进入到分支,git branch 可查看当前分支指针状态

#### 注
约定在Dev分支上面进行编码开发.上述的所有提交代码命令必须在dev分支上执行,最后的一句git push 换成以下命令

>$ git push -u origin dev:dev // 提交本地dev到远程dev.第一次远程无dev会自动创建dev

更新远程分支代码到本地:
>$ git fetch origin dev // fetch远程dev分支代码 ..避免使用pull

在当前分支合并fetch下面的代码
>$ git merge origin/dev  // 合并从远程dev分支fetch下来的代码

## 注意

版本正式上线后,需要将dev分支发布到Master分支.采用以下命令:
>$ git checkout master  // 切换到Master分支

>$ git merge --no-ff dev // 对Dev分支进行合并

>使用--no-ff参数后，会执行正常合并，在Master分支上生成一个新节点。为了保证版本演进的清晰，推荐采用这种做法


### Git4个阶段的撤销操作

>了解git阶段首选理解git的几个区:

>工作区(working area),

>暂存区(stage),

>本地仓库(local repository),

>远程仓库(remote repository).

>每将文件存到不同的区的时候会产生一个状态,在加上最开始的一个状态总共5个状态.

>未修改(Origin)

>已修改(Modified)

>已暂存(Staged)

>已提交(Committed)

>已推送(Pushed)

##### 1.文件处于已修改的状态,即修改过文件.未暂存(add)

>文件已修改,恢复到初始状态(未做任何修改状态)

>$git checkout . 或者 $git reset --hard origin/dev    // 恢复到与远程dev保持一致的状态,相当于刚clone dev的状态

##### 2.文件处于已暂存(stage),未提交(commit)

>文件已经进行过git add . 操作,但是还未进行git commit操作

>$git reset  // 恢复到已修改的状态

>$git checkout . // 继续执行这条,就恢复到初始状态(未做任何修改状态)

>如果要实现恢复到初始状态(未做任何修改状态),除了通过执行上面2步命令外,也可一直接执行下面这句,一步恢复到初始状态

>$git reset --hard // 一步到初始状态

##### 3.文件处于已提交(commit),未推送(push)

>这种情况下,代表已经提交到本地仓库了,既然已经污染了你的本地仓库，那么就从远程仓库把代码取回来吧.恢复到初始状态了,

>$git reset --hard origin/dev  //<b>直接恢复到初始化状态,但已做的修改全部会丢失</b>

##### 4.文件处于已推送(push)

>既git add了，又git commit了，并且还git push了，这时代码已经进入远程仓库。如果想恢复的话.由于本地仓库和远程仓库是等价的，只需要先恢复本地仓库，再强制push到远程仓库就好了

>$git reset --hard HEAD^   //将本地恢复到初始状态,<b>之前已做的修改全部会丢失</b>

>$git push -f // 将本地仓库初始化后推送到远程,将远程保持和本地一致

##### 注:只要还未影响到本地仓库(local repository)的时候,即没有commit时,都可以恢复到已修改的状态.一旦commit后,影响了本地仓库,就只能恢复到上一次的本地仓库的版本.所做的修改都会丢失..

###git撤销暂存区的文件
>有时候执行git add . 后,将当前目录下的所有改动文件都添加到了暂存区,此时如果有三两个文件是不需要添加进暂存区的,可以执行以下命令将文件从暂存区移除
>$git rm --cached 文件名

### git tag的常用使用

>Git可以对某个版本打上标签(tag)，表示本版本为发行版

>$git tag // 查看所有标签

>$git tag -l 1.0.*  // 打印符合检索条件的标签

>$git checkout 1.0.0 // 查看对应标签状态

>$git tag -a 1.0.0 -m "1.0.0版本" // 创建带备注标签(推荐)

>$git tag -a 1.0.0 0c3b62d -m "备注信息" // 针对特定commit版本SHA创建标签

>$git tag -d 1.0.0 // 删除本地1.0.0标签

>$git push origin --tags // 将本地所有标签发布到远程仓库

>$git push origin 1.0.0 // 指定标签版本(1.0.0)发送

>$git push origin --delete 1.0.0 // 删除远程仓库对应标签,此命令需要Git版本 > V1.7.0

>$git push origin :refs/tags/1.0.0 // 删除远程仓库对应标签,此命令需要Git版本 < V1.7.0
