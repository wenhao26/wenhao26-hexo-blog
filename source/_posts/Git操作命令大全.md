---
title: Git操作命令大全
categories: 
- 开发技能
tags: 
- Git
---

##### 整理了一些Git常用的命令，方便日常开发的使用（更多命令持续更新中...）。文章中的命令不是最详细的，可以帮助你快速去熟悉某些指令，具体详情用法，可以谷歌一下或者查阅官方文档。
<!--more-->

#### 基本命令

##### 空的项目中添加远程仓库

``` bash
echo "# test" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin <你的远程仓库>
git push -u origin main
```

##### 已有的项目中添加远程仓库

``` bash
git remote add origin <你的远程仓库>
git branch -M main
git push -u origin main
```

##### 将文件添加到暂存区

``` bash
#添加所有文件
git add .

#添加指定文件
git add <文件路径>
```

##### 提交

``` bash
git commit -m "描述"
```

##### 推送到远程仓库

``` bash
git push
或者
git push -u origin <分支名>
```

##### 从远程更新或拉取

``` bash
git pull
```

##### 版本回退

``` bash
#回退到上一个版本
git reset --hard HEAD^:

#回退到指定版本（(其中d7b5是想回退的指定版本号的前几位)）
git reset --hard d7b5:
```

##### 查看仓库操作历史记录

``` bash
git reflog
```

##### 重新指向仓库地址

``` bash
1. 删除项目中的仓库地址
git remote rm origin

2. 添加新的git地址
git remote add origin <你的远程仓库>

3. 设置本地develop分支关联远程develop分支
git fetch

4. 修改origin后面分支名称，如master分支可改为 origin/master master
git branch --set-upstream-to=origin/develop develop
git branch --set-upstream-to=origin/qa qa
git branch --set-upstream-to=origin/master master
git branch --set-upstream-to=origin/rebuild rebuild
```

##### 查看当前仓库地址

``` bash
git remote show origin
```

##### 找到历史提交的 commit ID

``` bash
git log --pretty=oneline --abbrev-commit
```

#### 分支相关命令

##### 查看当前分支

``` bash
git branch
```

##### 查看所有分支

``` bash
git branch -r
或者
git branch -a
```

##### 切换分支

``` bash
git checkout <指定分支名>
```

##### 分支合并（比如发布功能，需要将你的分支合并到main分支上）

``` bash
#将某个分支合并到当前分支上
git merge <指定分支名>
```

#### 标签相关命令

##### 列出tab列表

``` bash
git tag
```

##### 创建轻量级标签（没有附带其他的信息）

``` bash
git tag <标签名>
```

##### 创带注释的标签

``` bash
git tag -a <标签名> -m "描述"
```

##### 将标签推送到远程仓库（此步骤执行完，远程仓库将能看到打的标签）

``` bash
git push origin <标签名>
```

##### 删除标签

``` bash
git tag -d <标签名>
```

##### 删除远程标签

``` bash
git push origin :refs/tags/<标签名>
```

##### 根据标签回退版本（常用哦）

``` bash
1. 查看一下tag列表
git tag

2. 查看标签详情
git show <标签名>

3. git show命令可以看到打tag时提交的id，我们通过这个commit ID 回退代码
git reset --hard 7441b8
```

<br />

### ==更多命令持续更新中...==