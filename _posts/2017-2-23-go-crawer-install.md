---
layout: post
title:  "Pholcus（幽灵蛛）的安装与使用"
categories: go
tags:  go 爬虫
---


* content
{:toc}

Pholcus（幽灵蛛）是一款纯Go语言编写的支持分布式的高并发、重量级爬虫软件，定位于互联网数据采集，为具备一定Go或JS编程基础的人提供一个只需关注规则定制的功能强大的爬虫工具。

它支持单机、服务端、客户端三种运行模式，拥有Web、GUI、命令行三种操作界面；规则简单灵活、批量任务并发、输出方式丰富（mysql/mongodb/kafka/csv/excel等）、有大量Demo共享；另外它还支持横纵向两种抓取模式，支持模拟登录和任务暂停、取消等一系列高级功能。

<!--excerpt-->

## 下载安装

```go

go get -u -v github.com/henrylee2cn/pholcus

```

## 运行

直接liteide上面编译运行。gui的界面如下表示安装成功！
![](https://github.com/henrylee2cn/pholcus/raw/master/doc/guishow_0.jpg)