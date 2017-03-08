---
layout: post
title:  "linux下安装mongoDB"
categories: linux
tags:  mongoDB
---


* content
{:toc}

linux下安装mongoDB

<!--excerpt-->

## 下载安装

```go

curl -O https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-3.0.6.tgz    # 下载
tar -zxvf mongodb-linux-x86_64-3.0.6.tgz                                   # 解压

mv  mongodb-linux-x86_64-3.0.6/ /usr/local/mongodb                         # 将解压包拷贝到指定目录

export PATH=<mongodb-install-directory>/bin:$PATH        #MongoDB 的可执行文件位于 bin 目录下，所以可以将其添加到 PATH 路径中

```

## 创建数据库目录

MongoDB的数据存储在data目录的db目录下，但是这个目录在安装过程不会自动创建，所以你需要手动创建data目录，并在data目录中创建db目录。
以下实例中我们将data目录创建于根目录下(/)。
注意：/data/db 是 MongoDB 默认的启动的数据库路径(--dbpath)。

```go

mkdir -p /data/db

```

## 启动mongoDB


```go

$ cd /usr/local/mongodb/bin
$ ./mongo
MongoDB shell version: 3.0.6
connecting to: test
Welcome to the MongoDB shell.
……

```

## 图形化工具

http://pan.baidu.com/s/1bpotTRT





