---
title: 如何使用git
description: 总结&介绍Git基本操作
date: 2022-08-09
slug: git
image: git.jpg
categories:
    - 常用工具
tags:
    - Git
---

## 基本操作
```
git init // 初始化仓库，生成.git目录

git status // 查看仓库的状态，显示目前分支，以及有没有可疑提交的内容

git add 要添加的文件 // 向暂存区添加文件

git commit // 执行启动编辑器，可以记述更加详细的信息
git commit -m "本次的提交注释"// 添加对提交的概述；
git commit --amend // 修改上一条提交信息

git log // 查看提交日志
git log --pretty=short // 显示第一行简述信息
git log 文件名或目录名 //显示指定文件的日志
git log -p // 查看提交带来的变动，显示文件前后差别
git log -p 文件名或目录名 //查看指定文件的变动

git reflog // 查看当前仓库执行过的操作的日志

git diff // 查看当前工作树与暂存区的差别
git diff HEAD // 查看与最新提交的差别，在git commit前执行这条命令是个好习惯
```

## 分支的操作
```
git branch // 显示分支一览表

git checkout -b 新分支名 // 创建并切换到新的分支；
/以上命令与“git branch 新分支名”，再执行“git git checkout 新分支名”效果相同/

git checkout 分支名 // 切换到分支
git checkout - // 切换到上一个分支

git merge // 合并分支
git merge --no-ff A // 将分支A合并到当前分支，并启动编辑器录入合并提交的信息

git log --graph // 以图表形式查看分支
```

## 更改提交的操作
```
/使用git reflog 查看仓库日志，第一列为时间哈希值/

git reset // 回溯历史版本
git reset --hard 目标时间点的哈希值 // 恢复到时间点的状态，
```

## 推送至远程仓库
```
// 将远程仓库的名称设置为origin（标识符）
git remote add origin git@github.com:用户名/git-tutorial.git 

git push // 推送至远程仓库

git push -u origin master // 将当前分支内容推送给远程仓库origin的master分支；
// -u参数在推送的同时，将origin仓库的master分支设置为本地仓库当前分支的upstream（上游）,
// 运行git pull 命令，本地仓库的这个分支可以直接从origin的master分支获取内容
```

## 从远程仓库获取
```
git clone // 将github仓库clone到本地，注意不要与之前操作的仓库在同一目录下
git checkout -b A origin/A //以远程仓库分支A为来源，在本地新建分支A

git pull // 获取最新的远程仓库分支
```
## 其他深入理解Git的资料

- Pro Git (零基础的git学习资料)
- LearnGitBranching (学习git基本操作的网站)
- tryGit (只有英文版的网站)

> 如有遗漏或错误，欢迎补充纠正