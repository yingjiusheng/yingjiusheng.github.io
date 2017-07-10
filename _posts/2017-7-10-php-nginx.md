---
layout: post
title:  "nginx php 浏览器访问502超时解决办法(转)"
categories: Nginx
tags:  PHP Nginx
---

* content
{:toc}
php执行文件如果长时间没有返回结果，那么nginx报超时错误。

<!--excerpt-->
## 正文

-  最近网站在处理大数据时总是出现 504 Gateway Time-out,于是在网上找了一些资料

-  Nginx 502 Bad Gateway的含义是请求的PHP-CGI已经执行，但是由于某种原因(一般是读取资源的问题)没有执行完毕而导致PHP-CGI进程终止。
-Nginx 504 Gateway Time-out的含义是所请求的网关没有请求到，简单来说就是没有请求到可以执行的PHP-CGI。
-  解决这两个问题其实是需要综合思考的，一般来说Nginx 502 Bad Gateway和php-fpm.conf的设置有关，而Nginx 504 Gateway Time-out则是与nginx.conf的设置有关。
- 而正确的设置需要考虑服务器自身的性能和访客的数量等多重因素。
- 　以我目前的服务器为例子CPU是奔四1.5G的，内存1GB，CENTOS的系统，访客大概是50人左右同时在线。
- 　但是在线的人大都需要请求PHP-CGI进行大量的信息处理，因此我将nginx.conf设置为：

```

    fastcgi_connect_timeout 300s;
　　fastcgi_send_timeout 300s;
　　fastcgi_read_timeout 300s;
　　fastcgi_buffer_size 128k;
　　fastcgi_buffers 8 128k;#8 128
　　fastcgi_busy_buffers_size 256k;
　　fastcgi_temp_file_write_size 256k;
　　fastcgi_intercept_errors on;
　　
```

　　
- 这里最主要的设置是前三条，即

```
    fastcgi_connect_timeout 300s;
　　fastcgi_send_timeout 300s;
　　fastcgi_read_timeout 300s;
　　
```

　　
- 这里规定了PHP-CGI的连接、发送和读取的时间，300秒足够用了，因此我的服务器很少出现504 Gateway Time-out这个错误。最关键的是php-fpm.conf的设置，这个会直接导致502 Bad Gateway和504 Gateway Time-out。
- 下面我们来仔细分析一下php-fpm.conf几个重要的参数：
- php-fpm.conf有两个至关重要的参数，一个是”max_children”,另一个是”request_terminate_timeout”
- 我的两个设置的值一个是”40″，一个是”900″，但是这个值不是通用的，而是需要自己计算的。
- 计算的方式如下：
- 如果你的服务器性能足够好，且宽带资源足够充足，PHP脚本没有系循环或BUG的话你可以直接 将”request_terminate_timeout”设置成0s。0s的含义是让PHP-CGI一直执行下去而没有时间限制。而如果你做不到这一 点，也就是说你的PHP-CGI可能出现某个BUG，或者你的宽带不够充足或者其他的原因导致你的PHP-CGI能够假死那么就建议你 给”request_terminate_timeout”赋一个值，这个值可以根据你服务器的性能进行设定。一般来说性能越好你可以设置越高，20分钟 -30分钟都可以。由于我的服务器PHP脚本需要长时间运行，有的可能会超过10分钟因此我设置了900秒，这样不会导致PHP-CGI死掉而出现502 Bad gateway这个错误。
- 而”max_children”这个值又是怎么计算出来的呢?这个值原则上是越大越好，php-cgi的进程多了就会处理的很快，排队的请求就 会很少。设置”max_children”也需要根据服务器的性能进行设定，一般来说一台服务器正常情况下每一个php-cgi所耗费的内存在20M左 右，因此我的”max_children”我设置成40个，20M*40=800M也就是说在峰值的时候所有PHP-CGI所耗内存在800M以内，低于 我的有效内存1Gb。而如果我的”max_children”设置的较小，比如5-10个，那么php-cgi就会“很累”，处理速度也很慢，等待的时间 也较长。如果长时间没有得到处理的请求就会出现504 Gateway Time-out这个错误，而正在处理的很累的那几个php-cgi如果遇到了问题就会出现502 Bad gateway这个错误。
- fastcgi_read_timeout 300s;