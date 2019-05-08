---
title: git工作流
date: 2019-05-07 22:54:50
tags: 工具
---


![img](/images/git01.png)

### 前言







### 一、 分支介绍

#### 1.1 主分支（master）

​	主分支上包含线上正在运行的代码

 

#### 1.2 开发分支（develop）

​	开发分支是基于主分支创建的分支，主要用户提交我们日常开发，且经过测试通过的并准备上线的代码

 

#### 1.3 临时分支（feature/功能、bugfix/功能）

​	临时分支是基于develop或者master分支创建的，在使用完成后可直接delete的分支

- 特性分支（feature）

​            特性分支是基于开发分支（develop）创建的开发分支，主要用于完成某个功能模块的开发，开发完成后再次并入develop。

​	例如开发资讯功能，新建分支为： feature/information

- 修补Bug分支
  修补Bug分支是基于master创建的临时分支，主要用于修改线上的Bug，并要及时上线。bugfix分支测试通过后直接并入master分支。然后develop分支同步master分支代码，其他特性分支同步develop分支代码



#### 1.4 临时部署分支（publish）



### 二、工作流

#### 2.1 流程相关命令（一）

```
#本地创建develop分支
	git checkout --track origin/develop
#更新本地develop分支代码
	git pull origin develop 
#创建特性分支
	git checkout develop
	git checkout -b feature/social
#特性分支同步develop分支代码
	git rebase develop
#特性分支代码并入develop
    git checout develop
    git merge feature/social --squash
    git add .
    git commit -m "合并社保功能代码"
    git push origin develop
```



#### 2.2 流程相关命令（二）

```javascript
//-----------开始开发新功能-------------------
1.在远程基于dev branch 创建一个自已任务的新分支，feature/分支名， 如：feature/test_3
2.在本地切换至 dev branch, 基于当前的dev branch 拉取最新的内容：
	git checkout dev			//本地切换至 dev branch
	git pull					//些时新建的分支会在pull的内容中
3.在本地当创建分支同时使本地分支追踪远程分支：
	git checkout --track origin/feature/test_3
//-----------开发中-------------------（重复执行）	
4.基于当前分支做修改之后，添加并提交：
	git add .					//当前目录下所有新的文件添加到git中
	git commit -m '提交描述'		//打包到本地
	git pull					//拉取远程代码到本地
	git push					//提交到远程仓库
//-----------开发完成-------------------
5.提交之后切换至dev分支拉取最新的代码做merge,存在冲突解决，没有则push:
	git checkout dev			//本地切换至 dev branch
	git pull					//拉取代码
	git checkout feature/test_3	//本地切换至 feature/test_3
	git merge dev				//查看是否存在冲突
	git push					//推代码到远程
```



 

### 三、 常用命令

#### 3.1 简单使用

```
git add .
git commit
git push
git pull
git clone https://xxxxxxxxx.com
git rebase
git stash
git checkout dev
git checkout -
git branch
git merge dev
git log
```



#### 3.2 通过命令直接修改远程地址

进入git_test根目录 git remote 查看所有远程仓库， git remote xxx 查看指定远程仓库地址

```
git remote set-url origin http://192.168.100.235:9797/john/git_test.git
```



### 四、把连续的多个commit 整理成1个

[参考资料1](https://blog.csdn.net/u013276277/article/details/82470177)  [参考资料2](https://www.cnblogs.com/icepeach/p/5283479.html)

1. git log 	// 查看提交记录

2. git rebase -i commitId		 // commitId 需合并的上一个commit Id

   选择需要合并的commit

   > (1) 处理
   >
   > ​	pick 的意思是要会执行这个 commit
   > ​	squash 的意思是这个 commit 会被合并到前一个commit
   >
   > ​	:wq 保存修改
   >
   > (2) 编写新的commit
   >
   > ​	:wq保存修改

3. git push --force        // 合并完成后需要一次强制的push, 要加入`--force`覆盖掉github上的commit

   (不安全操作)



### 五、可视化客户端

#### 5.1 sourcetree

SourceTree 是 Windows 和Mac OS X 下免费的 [Git](http://lib.csdn.net/base/git) 和 Hg 客户端，拥有可视化界面，容易上手操作。同时它也是Mercurial和Subversion[版本控制](http://lib.csdn.net/base/git)系统工具。支持创建、提交、clone、push、pull 和merge等操作。



### 参考资料

1. [*Git*教程 - 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/896043488029600)
2. [git 几个commit点合并成一个commit点](https://blog.csdn.net/u013276277/article/details/82470177)
3. [git合并多个commit](https://www.cnblogs.com/icepeach/p/5283479.html)
4. [SourceTree详细使用教程](http://blog.cocoachina.com/article/71732)