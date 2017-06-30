---
layout: post
title:  "php修改文件不能立即展示结果缓存去除"
categories: PHP
tags:  PHP Linux Cache
---

* content
{:toc}
php修改文件不能立即展示结果缓存去除,ZendOpcache之类的opcode缓存原因。

<!--excerpt-->
## 说明
访问phpinfo(),看看是不是你开启了ZendOpcache之类的opcode缓存.ZendOpcache里面有个过期时间配置,如opcache.revalidate_freq=60,表示60秒后脚本再次被访问时会检测PHP文件的时间戳,有改变则更新opcode缓存,你可以设为0,这样每次访问都会检测文件时间戳,你的修改就能生效了.
## 实际操作命令

```
cd /usr/local/php/ect/php.d
sudo vim ext-opcache.ini 

```
修改 revalidate_freq=0

重启php