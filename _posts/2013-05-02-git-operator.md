---
layout: post
category : github
date: 2013/05/02 00:56:52 
title: git操作合集
tags : github
---


###概述
git是一个分布式代码管理工具，所以支持多个仓库，在git里，服务器上的仓库在本地称为remote。

###操作

###当在github上创建一个repository之后，会提示：

	Create a new repository on the command line
	touch README.mdß 
	git init 
	git add README.md 
	git commit -m "first commit" 
	git remote add origin https://github.com/liuyiyou/gittest.git    //ssh  :git@github.com:liuyiyou/gittest.git
	git push -u origin master



###将一个本地仓库的代码提交到远程库
1. 本地add
	
2. 本地提交
	
3. 远程add //这步可能没有，因为已经存在了。

4. push到远程仓库

###代码
	git add .  // git add --all
	git commit -m "xxx"
	git remote add master https://github.com/liuyiyou/liuyiyou.github.io
	git push -u origin master

###通过命令新建一个版本库并将该版本库提交到远程版本库中
1. 新建目录

2. 将路径跳转到该目录下（）
	
3. 新建一个文件（）
	
4. 在该目录下新建版本库()   //添加完后，该目录下多了一个.git目录
	
5. 将该文件添加到本地版本库中( )
	
6. 将该版本库添加到github上()
	
7. 推送到github()//到这一步才能在github上看到提交的文件.
	
###代码
	git mkdir gittest
	git cd gittest
	touch README.md 
	git init 
	git add README.md
	git remote add origin https://github.com/liuyiyou/gittest.git  ||git@github.com:liuyiyou/gittest.git
	git push -u origin master


###通过命令行将一个已经从在的版本库推送到github中
	git remote add origin https://github.com/liuyiyou/gittest.git 
	git push -u origin master

###查看代码状态
1：查看版本库代码状态    
	git status

2：查看修改内容
	git diff  reademe.txt

3：将修改后的内容添加到仓库
	git add reademe.txt

4：将文件提交
	git commit -m ""

###版本回退
1：查看历史记录

	git log  || git log --pretty=oneline

2：版本回退

2.1 回到之前的某版本
	
	git reset --hard HEAD^

上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。

4：回到过去版本之后又想回原来的版本
	
	git reset --hard  commit id  其中commitId可以通过git log得到

3：记录你的每一次命令：
	
	git reflog

###工作区和缓存区
工作区（Working Directory）：就是你在电脑里能看到的目录，比如我的gittes文件夹就是一个工作区：

版本库（Repository）：工作区有一个隐藏目录“.git”，这个不算工作区，而是Git的版本库。
Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD。

创建Git版本库时，Git自动为我们创建了唯一一个master分支，所以，现在，commit就是往master分支上提交更改。简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。

增加一个文件LICENSE同时修改一个文件README.md
	git status 会提示一个已经修改一个尚未添加

	git add LICENSE
	git add README.md
	git status 

git add命令实际上就是把要提交的所有修改放到暂存区（Stage），然后，执行git commit就可以一次性把暂存区的所有修改提交到分支。


###管理修改
暂时略

###分支管理
1：创建分支并切换分支
	$ git checkout -b dev

创建分支
	$ git branch dev

提交分支
	$ git checkout dev

查看分支
	git branch

合并分支

	git merge dev

删除分支：

	git branch -d dev

切换分支：

	git checkout name


合并某分支到当前分支：

	git merge name


###其他操作
直接clone一个仓库
	git clone git@search.ude.taobao.net:project/search.git

另外一种clone


删除.git
	rm -rf .git


add多个文件
	git add .
	git add --all






###添加远程仓库路径

	$git remote add github git@github.com:liuyiyou/blog.git

###实际上，pull就是fetch+merge
	$:git pull github --all --tags

###把工作目录迁移到github上面：
	git remote add github git@github.com:liuyiyou/blog
	git push github --all --tags


###显示所有的远程仓库

