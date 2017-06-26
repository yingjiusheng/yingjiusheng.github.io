---
layout: post
title:  "微信公众平台开发（二）"
categories: PHP
tags:  PHP Weixin
---

* content
{:toc}
此篇为微信公众号平台的开发，进一步内容深入。引用php-weixin-sdk类

<!--excerpt-->

## 控制器代码部分功能示例
   
   
```
<?php
/**
 * 微信个人订阅号地址入口
 * 724459928@qq.com
 */
namespace Script\Controller;

use Common\Controller\ScriptController;
use Script\Lib\Weixin\Wechat;

class WeixinController extends ScriptController
{
    /**
     * [init]: 获取wechat类
     * @return Wechat
     */
    public function init()
    {
        $options = array(
            'token' => 'ying', // 填写你设定的key
            // 'encodingaeskey'=>'VUGaDqIUmwEWzHRtGUZWcUrHZA0q0toPJDl0GPkD2hF',
            'appid' => 'wx53a68b7bce8f3657', // 填写高级调用功能的appid
            'appsecret' => '60cf6f5e236b324186a20405fa053c40' // 填写高级调用功能的密钥
        );
        $Wechat = new Wechat($options);
        return $Wechat;
    }

    /**
     * [index]:微信调用地址入口
     * User: jsying@iflytek.com
     */
    public function index()
    {
        $Wechat = $this->init();
        $Wechat->valid();
        /* 获取消息类型 */
        $type = $Wechat->getRev()->getRevType();
        switch ($type) {
            /* 文本类型消息回复 */
            case Wechat::MSGTYPE_TEXT :
                $key = $Wechat->getRev()->getRevContent();
                $text = '您输入的内容是:' . $key;
                $Wechat->text($text)->reply();
                exit ();
                break;
            /* 事件类型消息响应 */
            case Wechat::MSGTYPE_EVENT :
                $eventype = $Wechat->getRev()->getRevEvent();
                if ($eventype ['event'] == "CLICK") {
                    if ($eventype ['key'] == 'MENU_EVENT_1') {
                        $newsArr = array(
                            array(
                                'Title' => '图文1',
                                'Description' => '描述1',
                                'PicUrl' => 'http://www.imooc.com/static/img/common/logo.png',
                                'Url' => 'http://www.imooc.com'
                            )
                        );
                        $Wechat->news($newsArr)->reply();
//                        $Wechat->-text("这是单图文事件的回复")->reply();
                        exit ();
                    }
                    if ($eventype ['key'] == 'MENU_EVENT_2') {
                        $newsArr = array(
                            array(
                                'Title'=>'imooc',
                                'Description'=>"imooc is very cool",
                                'PicUrl'=>'http://www.imooc.com/static/img/common/logo.png',
                                'Url'=>'http://www.imooc.com',
                            ),
                            array(
                                'Title'=>'hao123',
                                'Description'=>"hao123 is very cool",
                                'PicUrl'=>'https://www.baidu.com/img/bdlogo.png',
                                'Url'=>'http://www.hao123.com',
                            ),
                            array(
                                'Title'=>'qq',
                                'Description'=>"qq is very cool",
                                'PicUrl'=>'http://www.imooc.com/static/img/common/logo.png',
                                'Url'=>'http://www.qq.com',
                            ),
                        );
                        $Wechat->news($newsArr)->reply();
//                        $Wechat->text("这是多图文事件的回复")->reply();
                        exit ();
                    }
                } elseif ($eventype['event'] == "subscribe") {
                    /* 关注事件 */
                    /* 获取头像等信息 可成功 */
                    $openid = $Wechat->getRevFrom();
                    $wx_info = $Wechat->getUserInfo($openid);
                    $text = '欢迎您【' . $wx_info['nickname'] . '】关注本订阅号！';
//                    file_put_contents('a_weixin.txt',$user['wx_info']);
                    $Wechat->text($text)->reply();
                }
                exit ();
                break;
            default :
                $Wechat->text("help info")->reply();
        }
    }

    /**
     * [createMenu]: 创建菜单
     * User: jsying@iflytek.com
     */
    public function createMenu()
    {
        $Wechat = $this->init();
        $newmenu = array(
            'button' => array(
                0 => array(
                    'name' => '扫码',
                    'sub_button' => array(
                        0 => array(
                            'type' => 'scancode_waitmsg',
                            'name' => '扫码带提示',
                            'key' => 'rselfmenu_0_0',
                        ),
                        1 => array(
                            'type' => 'scancode_push',
                            'name' => '扫码推事件',
                            'key' => 'rselfmenu_0_1',
                        ),
                    ),
                ),
                1 => array(
                    'name' => '发图',
                    'sub_button' => array(
                        0 => array(
                            'type' => 'pic_sysphoto',
                            'name' => '系统拍照发图',
                            'key' => 'rselfmenu_1_0',
                        ),
                        1 => array(
                            'type' => 'pic_photo_or_album',
                            'name' => '拍照或者相册发图',
                            'key' => 'rselfmenu_1_1',
                        ),
                        2 => array(
                            'type' => 'click',
                            'name' => '单图文回复',
                            'key' => 'MENU_EVENT_1',
                        ),
                        3 => array(
                            'type' => 'click',
                            'name' => '多图文回复',
                            'key' => 'MENU_EVENT_2',
                        ),
                        4 => array(
                            'type' => 'view',
                            'name' => 'click链接',
                            'url' => 'https://www.baidu.com',
                        ),
                    ),
                ),
                2 => array(
                    'type' => 'location_select',
                    'name' => '发送位置',
                    'key' => 'rselfmenu_2_0'
                ),
            ),
        );
        $result = $Wechat->createMenu($newmenu);
        if ($result['code'] == 0)
            die ("重新创建菜单成功!");
        else
            die ($result['code'] . ":" . $result['msg']);
    }
}
```