---
layout: post
title:  "nginx-安装与配置"
categories: nginx
tags:  nginx
---


* content
{:toc}

学习任何东西，首先要会使用他。代码也是如此，最近在研究nginx，以前只是简单的使用它，现在准备从头开始研究它，下面开始nginx的安装与配置。

<!--excerpt-->

## 安装前准备

选首先安装这几个软件：GCC，PCRE（Perl Compatible Regular Expression），zlib，OpenSSL。

Nginx是C写的，需要用GCC编译；Nginx的Rewrite和HTTP模块会用到PCRE；Nginx中的Gzip用到zlib；

用命令“# gcc”，查看gcc是否安装；如果出现“gcc: no input files”信息，说明已经安装好了。
否则，就需要用命令“# yum install gcc”，进行安装了！一路可能需要多次输入y，进行确认。
安装好后，可以再用命令“#gcc”测试，或者用命令“# gcc -v”查看其版本号。

同样方法，用如下命令安装PCRE，zlib，OpenSSL（其中devel，是develop开发包的意思）：

```go

# yum install -y pcre pcre-devel
# yum install -y zlib zlib-devel
# yum install -y openssl openssl-devel

```

## 下载并安装

下载，解压，配置，编译，安装

```go
# wget http://nginx.org/download/nginx-1.10.3.tar.gz
# tar xzf nginx-1.10.3.tar.gz
# cd nginx-1.10.3
# ./configure
# make
# make install

```
默认的安装路径为：/usr/local/nginx，到sbin目录下面，便可以启动、关闭nginx服务。
这里我们最常用的：  `./nginx -h` ，用来查看nginx所支持的命令和服务。

```go

nginx version: nginx/1.10.3
Usage: nginx [-?hvVtTq] [-s signal] [-c filename] [-p prefix] [-g directives]

Options:
  -?,-h         : this help
  -v            : show version and exit
  -V            : show version and configure options then exit
  -t            : test configuration and exit
  -T            : test configuration, dump it and exit
  -q            : suppress non-error messages during configuration testing
  -s signal     : send signal to a master process: stop, quit, reopen, reload
  -p prefix     : set prefix path (default: /usr/local/nginx/)
  -c filename   : set configuration file (default: conf/nginx.conf)
  -g directives : set global directives out of configuration file

```
## 添加到系统服务

编辑文件：`# vim /etc/init.d/nginx`，输入一下内容：

```go

#!/bin/sh
# chkconfig: 2345 85 15
# Startup script for the nginx Web Server
# description: nginx is a World Wide Web server.
# It is used to serve HTML files and CGI.
# processname: nginx
# pidfile: /usr/local/nginx/logs/nginx.pid
# config: /usr/local/nginx/conf/nginx.conf

PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
DESC="nginx deamon"
NAME=nginx
DAEMON=/usr/local/nginx/sbin/$NAME
SCRIPTNAME=/etc/init.d/$NAME

test -x $DAEMON || exit 0

d_start(){
  $DAEMON || echo -n "already running"
}

d_stop(){
  $DAEMON -s quit || echo -n "not running"
}


d_reload(){
  $DAEMON -s reload || echo -n "can not reload"
}

case "$1" in
start)
  echo -n "Starting $DESC: $NAME"
  d_start
  echo "."
;;
stop)
  echo -n "Stopping $DESC: $NAME"
  d_stop
  echo "."
;;
reload)
  echo -n "Reloading $DESC conf..."
  d_reload
  echo "reload ."
;;
restart)
  echo -n "Restarting $DESC: $NAME"
  d_stop
  sleep 2
  d_start
  echo "."
;;
*)
  echo "Usage: $ScRIPTNAME {start|stop|reload|restart}" >&2
  exit 3
;;
esac

exit 0

```

然后设置文件可执行：`# chmod +x /etc/init.d/nginx`，加入系统服务。
这时就可以用如下命令开启或者关闭：

```go

# service nginx start
# service nginx stop
# service nginx restart

```

## 设置开机启动

```go
# chkconfig --add nginx
# chkconfig nginx on/off
# chkconfig --list nginx
nginx 0:off 1:off 2:on 3:on 4:on 5:on 6:off

```
## 检测是否安装成功

首先查看服务器ip地址，`# ifconfig`，直接访问ip地址，如果现实如下`Welcome to nginx!`界面，说明安装成功。
如果访问不成功，这时要查看下防火墙是否关闭。

```go

vi /etc/sysconfig/iptables;
-A INPUT -m state –state New tcp -m tcp -p tcp –dport 80 -j ACCEPT
重新启动防火墙；
service iptables restart;


```
然后重启nginx。





