---
layout: post
title:  "Samba服务器的基本配置"
categories: Linux
tags:  Linux Samba
---

* content
{:toc}
Samba服务器的基本配置

<!--excerpt-->
# Samba服务器
## 步骤
1. 关闭防火墙

```
service iptables stop
```
2. 关闭SELinux

```
setenforce 0
```
3. 安装Samba和samba-client

```
yum install -y samba samba-client
```
4.添加用户

```
useradd test
pdbtest -a test
```
5.启动samba服务

```
chkconfig smb on #添加开机启动
service smb start #立即启动
```
6.测试
- 在window中我的电脑中打开
- 地址栏输入\\\\IP
- 右键映射成驱动器

7.设置共享目录

*安装后，默认目录为home*

- 创建共享目录并修改权限

```
mkdir -p /var/www/html
chmod -R 777 /var/www/html
```

- 修改配置文件:/etc/samba/smb.conf

添加如下内容：

```
[html]                      #共享目录名称
    path = /var/www/html    #共享目录位置
    browseable = yes        #是否可以浏览
    writable = yes          #是否可以写入
    public = no             #是否公开或者公共
```

