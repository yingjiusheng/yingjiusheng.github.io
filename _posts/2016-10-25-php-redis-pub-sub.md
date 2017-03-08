---
layout: post
title:  "php-redis发布/订阅"
categories: PHP
tags:  PHP redis
---

* content
{:toc}
php下redis的发布订阅实践

<!--excerpt-->


## Redis 发布订阅简介

Redis 发布订阅(pub/sub)是一种消息通信模式：发送者(pub)发送消息，订阅者(sub)接收消息。

Redis 客户端可以订阅任意数量的频道。当有新消息通过publish命令发送到频道时，这个消息就会被发送到订阅它的客户端。


## Redis 发布订阅命令

| 序号 | 命令及描述 |
|--------|--------|
|1|	PSUBSCRIBE pattern [pattern ...] 订阅一个或多个符合给定模式的频道。
|2|	PUBSUB subcommand [argument [argument ...]] 查看订阅与发布系统状态。
|3| PUBLISH channel message 将信息发送到指定的频道。
|4| PUNSUBSCRIBE [pattern [pattern ...]] 退订所有给定模式的频道。
|5|	SUBSCRIBE channel [channel ...] 订阅给定的一个或多个频道的信息。
|6| UNSUBSCRIBE [channel [channel ...]] 指退订给定的频道。

## 实例
  1.向频道发送信息(shell)

```php
redis> publish msg  “推送公告”
                (interger) 0
```

  2.向频道发送信息(php-api)

```php
$redis = new Redis(); 
$redis->connect('127.0.0.1',6379); 
$redis->publish('msg', '节操一屏幕');
```

 3.订阅频道(shell)

```php
redis> subscribe msg msg_1
Reading messages... (press Ctrl-C to quit)
1) "subscribe"       # 返回值的类型：显示订阅成功
2) "msg"             # 订阅的频道名字
3) (integer) 100       # 目前已订阅的频道数量
```

4.订阅频道（php-api）

```php
$redis = new Redis(); 
$redis->connect('127.0.0.1',6379); 
$redis->subscribe(array('msg'), 'callback'); 
function callback($instance, $channelName, $message) { 
 echo $channelName, "==>", $message,PHP_EOL; 
}
```

