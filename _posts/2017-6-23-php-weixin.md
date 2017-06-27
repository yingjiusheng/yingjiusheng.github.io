---
layout: post
title:  "微信公众平台开发（一）"
categories: PHP 微信
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

## 订阅事件后发送消息信息

```
    /**
     * [index]:微信认证接口
     * User: jsying@iflytek.com
     */
    public function index(){
        //获得参数 signature nonce token timestamp echostr
        $nonce     = $_GET['nonce'];
        $token     = 'ying';
        $timestamp = $_GET['timestamp'];
        $echostr   = $_GET['echostr'];
        $signature = $_GET['signature'];
        //形成数组，然后按字典序排序
        $array = array($nonce, $timestamp, $token);
        sort($array);
        //拼接成字符串,sha1加密 ，然后与signature进行校验
        $str = sha1( implode( $array ) );
        //不是第一次接入的时候将不会传送echostr参数
        if( $str  == $signature && $echostr ){
            //第一次接入weixin api接口的时候
            echo  $echostr;
            exit;
        }else{
            $this->reponseMsg();
        }
    }
    
    // 接收事件推送并回复
    public function reponseMsg(){
        //1.获取到微信推送过来post数据（xml格式）
        $postArr = $GLOBALS['HTTP_RAW_POST_DATA'];
        //2.处理消息类型，并设置回复类型和内容
        //收到内容格式
        /*<xml>
<ToUserName><![CDATA[toUser]]></ToUserName>
<FromUserName><![CDATA[FromUser]]></FromUserName>
<CreateTime>123456789</CreateTime>
<MsgType><![CDATA[event]]></MsgType>
<Event><![CDATA[subscribe]]></Event>
</xml>*/
        $postObj = simplexml_load_string( $postArr );
        //$postObj->ToUserName = '';
        //$postObj->FromUserName = '';
        //$postObj->CreateTime = '';
        //$postObj->MsgType = '';
        //$postObj->Event = '';
        // gh_e79a177814ed
        //判断该数据包是否是订阅的事件推送
        if( strtolower( $postObj->MsgType) == 'event'){
            //如果是关注 subscribe 事件
            if( strtolower($postObj->Event == 'subscribe') ){
                //回复用户消息(纯文本格式)
                $toUser   = $postObj->FromUserName;
                $fromUser = $postObj->ToUserName;
                $time     = time();
                $msgType  =  'text';
                $content  = '欢迎关注我们的微信公众账号'.$postObj->FromUserName.'-'.$postObj->ToUserName;
                $template = "<xml>
							<ToUserName><![CDATA[%s]]></ToUserName>
							<FromUserName><![CDATA[%s]]></FromUserName>
							<CreateTime>%s</CreateTime>
							<MsgType><![CDATA[%s]]></MsgType>
							<Content><![CDATA[%s]]></Content>
							</xml>";
                $info     = sprintf($template, $toUser, $fromUser, $time, $msgType, $content);
                echo $info;
                //回复内容格式
                /*<xml>
                <ToUserName><![CDATA[toUser]]></ToUserName>
                <FromUserName><![CDATA[fromUser]]></FromUserName>
                <CreateTime>12345678</CreateTime>
                <MsgType><![CDATA[text]]></MsgType>
                <Content><![CDATA[你好]]></Content>
                </xml>*/
            }
        }
    }
```



## 接收发送简单文本消息

```
if(strtolower($postObj->MsgType) == 'text'){
            switch( trim($postObj->Content) ){
                case 1:
                    $content = '您输入的数字是1';
                break;
                case 2:
                    $content = '您输入的数字是2';
                break;
                case 3:
                    $content = '您输入的数字是3';
                break;
                case 4:
                    $content = "<a href='http://www.imooc.com'>慕课</a>";
                break;
                case '英文':
                    $content = 'imooc is ok';
                break;

            }
                $template = "<xml>
<ToUserName><![CDATA[%s]]></ToUserName>
<FromUserName><![CDATA[%s]]></FromUserName>
<CreateTime>%s</CreateTime>
<MsgType><![CDATA[%s]]></MsgType>
<Content><![CDATA[%s]]></Content>
</xml>";
//注意模板中的中括号 不能少 也不能多
                $fromUser = $postObj->ToUserName;
                $toUser   = $postObj->FromUserName;
                $time     = time();
                $msgType  = 'text';
                echo sprintf($template, $toUser, $fromUser, $time, $msgType, $content);
        }
```

## 回复单多图文

```
        //用户发送tuwen1关键字的时候，回复一个单图文
        if( strtolower($postObj->MsgType) == 'text' && trim($postObj->Content)=='tuwen2' ){
            $toUser = $postObj->FromUserName;
            $fromUser = $postObj->ToUserName;
            $arr = array(
                array(
                    'title'=>'imooc',
                    'description'=>"imooc is very cool",
                    'picUrl'=>'http://www.imooc.com/static/img/common/logo.png',
                    'url'=>'http://www.imooc.com',
                ),
                array(
                    'title'=>'hao123',
                    'description'=>"hao123 is very cool",
                    'picUrl'=>'https://www.baidu.com/img/bdlogo.png',
                    'url'=>'http://www.hao123.com',
                ),
                array(
                    'title'=>'qq',
                    'description'=>"qq is very cool",
                    'picUrl'=>'http://www.imooc.com/static/img/common/logo.png',
                    'url'=>'http://www.qq.com',
                ),
            );
            $template = "<xml>
						<ToUserName><![CDATA[%s]]></ToUserName>
						<FromUserName><![CDATA[%s]]></FromUserName>
						<CreateTime>%s</CreateTime>
						<MsgType><![CDATA[%s]]></MsgType>
						<ArticleCount>".count($arr)."</ArticleCount>
						<Articles>";
            foreach($arr as $k=>$v){
                $template .="<item>
							<Title><![CDATA[".$v['title']."]]></Title>
							<Description><![CDATA[".$v['description']."]]></Description>
							<PicUrl><![CDATA[".$v['picUrl']."]]></PicUrl>
							<Url><![CDATA[".$v['url']."]]></Url>
							</item>";
            }

            $template .="</Articles>
						</xml> ";
            echo sprintf($template, $toUser, $fromUser, time(), 'news');

            //注意：进行多图文发送时，子图文个数不能超过10个
        }
```


