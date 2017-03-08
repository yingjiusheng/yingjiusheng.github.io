---
layout: post
title:  "CentOS下安装redis"
categories: linux
tags:  linux redis
---

* content
{:toc}
CentOS6.5下安装redis
<!--excerpt-->

## 安装方法

```php
    wget http://download.redis.io/redis-stable.tar.gz
    tar xvzf redis-stable.tar.gz
    cd redis-stable
    make
```
在make成功之后，会在src的目录下多出一些可执行文件，redis-server，redis-cli等
用cp复制到usr目录下运行。

```php
    cp redis-server /usr/local/bin/

    cp redis-cli /usr/local/bin/
```

新建目录，存放配置文件

```php
    mkdir /etc/redis

    mkdir /var/redis

    mkdir /var/redis/log

    mkdir /var/redis/run

    mkdir /var/redis/6379
```
在redis解压根目录中找到配置文件模板，复制到如下位置。

```php
    cp redis.conf /etc/redis/6379.conf
```


通过vim命令修改
```php
    daemonize yes

    pidfile /var/redis/run/redis_6379.pid

    logfile /var/redis/log/redis_6379.log

    dir /var/redis/6379

```


最后运行redis：

`$ redis-server /etc/redis/6379.conf`

## 可能遇到的问题
异常一：

make[2]: cc: Command not found

异常原因：没有安装gcc

解决方案：yum install gcc-c++


异常二：

zmalloc.h:51:31: error: jemalloc/jemalloc.h: No such file or directory

异常原因：一些编译依赖或原来编译遗留出现的问题

解决方案：make distclean。清理一下，然后再make。


在make成功以后，需要make test。在make test出现异常。

异常一：

couldn't execute "tclsh8.5": no such file or directory

异常原因：没有安装tcl

解决方案：yum install -y tcl。



