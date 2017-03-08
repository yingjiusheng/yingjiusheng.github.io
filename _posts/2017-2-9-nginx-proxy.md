---
layout: post
title:  "nginx-反向代理和负载均衡"
categories: nginx
tags:  nginx
---


* content
{:toc}

**反向代理**是指以代理服务器来接受客户端发来的请求，然后将请求转发到网络的其他服务器上面，并将服务器的结果返回给连接的客户端。代理服务器对外就表现为一个服务器。
**负载均衡**是由多台服务器以对称的方式组成服务器集合，每台服务器都具有同等的位置，都可以单独对外提供服务无需其他服务器辅助。
nginx作为一个轻量级、高性能的web服务器，能够很容易实现负载均衡。通过简单的配置文件就能作为反向代理服务器实现负载均衡。

<!--excerpt-->

## 准备条件

四台服务器（这里通过虚拟机实现）
1.192.168.154.131（负载均衡服务器）
2.192.168.154.132（web服务器）
3.192.168.154.133（web服务器）
4.192.168.154.131（web服务器）

四台服务器都安装nginx软件（不必每台服务器都安装一边环境，安装一台，然后克隆即可实现）

## web服务器修改

web服务器不必修改多少内容，只需显示出每次访问的服务器不同即可（说明每次访问的服务器都是通过负载均衡服务器代理过的）
我这里简单修改了 ` html/index.html ` 文件。

## 配置负载均衡服务器
修改文件 nginx.conf 内容如下：

```go

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;
    upstream 192.168.154.131 {
       server 192.168.154.132:80;
       server 192.168.154.133:80;
       server 192.168.154.134:80;
    }
    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
            proxy_pass        http://192.168.154.131/;
            proxy_set_header  X-Real-IP  $remote_addr;
            client_max_body_size  100m;
        }
    #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
     ｝
    ｝

```

浏览器打开：192.168.154.131，如果132,133,134内容交替现实表明实验成功。
