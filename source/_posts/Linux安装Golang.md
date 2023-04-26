---
title: Linux安装Golang
categories: 
- 运维
tags: 
- Golang
---

##### Golang是Google开发的一款编程语言，它融合了静态语言和脚本语言的优点，在性能和开发效率上有很大的优势，成为近年来最热门的编程语言之一。本文章分享如何在Linux下安装Golang以及一些配置项的说明。
<!--more-->

#### 下载Golang

##### 到官网下载你想要的版本，我推荐尽可能使用新版本
* [https://go.dev/dl](https://go.dev/dl/)
* [go1.18.10.linux-amd64.tar.gz](https://dl.google.com/go/go1.18.10.linux-amd64.tar.gz)
* 你也可以通过 wget 方式在你服务直接下载

<br />

#### 下载好安装包后，解压到安装目录

##### 如本案例解压到目录：/usr/local/go，这些你可以根据自己情况解压到对应目录即可。

``` bash
tar -C /usr/local -zxvf go1.18.10.linux-amd64.tar.gz
```

<br />

#### 配置 profile 

##### 需要在 /etc/profile 文件中，配置你Golang的一些信息。进入配置文件：

``` bash
vim /etc/profile
```

##### 在文件最后那里（哪里都可以的，新的配置，我习惯放到最后，配置Golang信息：

``` bash
# Golang
export GOROOT=/usr/local/go
export PATH=$PATH:$GOROOT/bin
```

##### 编辑好Golang配置项后，记得保存并且重新加载配置源

``` bash
source /etc/profile
```

#### 验证安装结果

##### 可以通过 go version 命令来验证Golang是否安装成功，成功的话，会出现以下结果：

``` bash
go version

go version go1.18.10 linux/amd64
```