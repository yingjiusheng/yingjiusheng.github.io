---
layout: post
title:  "微信公众平台开发"
categories: PHP
tags:  PHP Weixin
---

* content
{:toc}
此篇为微信公众号平台的开发，个人订阅号，而非服务号，企业号。

<!--excerpt-->

## 说明
   个人订阅号，开发者模式，订阅号为 ：YJss；
   
## 个人订阅号申请
-     未注册邮箱账号
-     公网可访问地址

## 个人订阅号开发者配置
### 启用开发者模式

### 配置服务器资料

-     服务器地址：公网可访问地址
-     Token：自定义token
-     随机生成
-     兼容模式
 
### 服务器提交验证时会提示token验证失败

 - 提交地址需要验证，地址响应代码如下

```
    /**
     * [index]:微信认证接口
     * User: jsying@iflytek.com
     */
    function index()
    {
        //1.将timestamp,nnce,token按字典排序
        $timestamp = $_GET['timestamp'];
        $nonce = $_GET['nonce'];
        $token = 'yingjs';
        $signature = $_GET['signature'];
        $array = array($timestamp, $nonce, $token);
        sort($array);
        //2,将排序后的三个参数拼接之后用sha1加密
        $tmpstr = implode('', $array);
        $tmpstr = sha1($tmpstr);
        //3,将加密后的字符串于signature进行对比，判断该请求是否来自微信
        if ($tmpstr == $signature) {
            echo $_GET['echostr'];
            exit;
        }
    }
```
### 再次提交完成服务器配置
