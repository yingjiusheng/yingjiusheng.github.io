---
layout: post
title:  "curl间接登录51Job网站企业中心"
categories: PHP
tags:  PHP Spider
---

* content
{:toc}
php利用curl间接登录51job网站企业中心

<!--excerpt-->
## 说明
企业中心登陆涉及到验证码图片问题，没有解决。手动登陆后截取cookie内容，放在请求头部可间接实现伪登录。所用工具postman调试。
## 控制器
直接上代码

```
<?php
/**
 * 51job简历爬虫程序入口
 * User: yingjs@foxmail.com
 * Date: 2017/6/13 0013
 */
namespace Script\Controller;
use Common\Controller\ScriptController;

class SpiderController extends ScriptController{

    public function _initialize()
    {
        parent::_initialize();
    }

    /**
     * [index]:爬虫入口
     * User: jsying@iflytek.com
     */
    function index(){
        echo $this->useIp();
        $url = 'http://ehire.51job.com/InboxResume/InboxRecentEngine.aspx';
//       此处cookie从浏览器下方header里复制出来的内容
        $cookie  = 'guid=14884341582773240050; EhireGuid=c5b2e0c128784aacb78637339e418ac5; search=jobarea%7E%60000000%7C%21ord_field%7E%600%7C%21recentSearch0%7E%601%A1%FB%A1%FA000000%2C00%A1%FB%A1%FA000000%A1%FB%A1%FA0000%A1%FB%A1%FA00%A1%FB%A1%FA9%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FAPHp%A1%FB%A1%FA2%A1%FB%A1%FA%A1%FB%A1%FA-1%A1%FB%A1%FA1498113338%A1%FB%A1%FA0%A1%FB%A1%FA%A1%FB%A1%FA%7C%21recentSearch1%7E%601%A1%FB%A1%FA000000%2C00%A1%FB%A1%FA000000%A1%FB%A1%FA0000%A1%FB%A1%FA00%A1%FB%A1%FA2%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA%A1%FB%A1%FA2%A1%FB%A1%FA%A1%FB%A1%FA-1%A1%FB%A1%FA1498111889%A1%FB%A1%FA0%A1%FB%A1%FA%A1%FB%A1%FA%7C%21recentSearch2%7E%601%A1%FB%A1%FA000000%2C00%A1%FB%A1%FA000000%A1%FB%A1%FA0000%A1%FB%A1%FA00%A1%FB%A1%FA9%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA%A1%FB%A1%FA2%A1%FB%A1%FA%A1%FB%A1%FA-1%A1%FB%A1%FA1497922825%A1%FB%A1%FA0%A1%FB%A1%FA%A1%FB%A1%FA%7C%21recentSearch3%7E%601%A1%FB%A1%FA000000%2C00%A1%FB%A1%FA000000%A1%FB%A1%FA0000%A1%FB%A1%FA00%A1%FB%A1%FA9%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA01%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA%A1%FB%A1%FA2%A1%FB%A1%FA%A1%FB%A1%FA-1%A1%FB%A1%FA1498098589%A1%FB%A1%FA0%A1%FB%A1%FA%A1%FB%A1%FA%7C%21recentSearch4%7E%601%A1%FB%A1%FA000000%2C00%A1%FB%A1%FA000000%A1%FB%A1%FA0000%A1%FB%A1%FA00%A1%FB%A1%FA9%A1%FB%A1%FA99%A1%FB%A1%FA01%A1%FB%A1%FA01%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA%A1%FB%A1%FA2%A1%FB%A1%FA%A1%FB%A1%FA-1%A1%FB%A1%FA1498098584%A1%FB%A1%FA0%A1%FB%A1%FA%A1%FB%A1%FA%7C%21collapse_expansion%7E%601%7C%21; nsearch=jobarea%3D%26%7C%26ord_field%3D%26%7C%26recentSearch0%3D%26%7C%26recentSearch1%3D%26%7C%26recentSearch2%3D%26%7C%26recentSearch3%3D%26%7C%26recentSearch4%3D%26%7C%26collapse_expansion%3D; _ujz=ODczNTIxMDgw; ps=us%3DWWIFaAN%252FUGMFel8yBmZcagEsV2dbbVI3XSdTMFtlAzsLOgBtD2tUZlY%252BD2ZVMVRhAjFQZlAqU35RFgEBDHRVDA%253D%253D%26%7C%26needv%3D0; slife=resumeguide%3D1%26%7C%26indexguide%3D1%26%7C%26loginwarning%3D1%26%7C%26lowbrowser%3Dnot%26%7C%26lastlogindate%3D20170627; 51job=cenglish%3D0; ASP.NET_SessionId=13uelqzdhqtnwwzd5xyrwp4r; HRUSERINFO=CtmID=3410587&DBID=3&MType=08&HRUID=4169208&UserAUTHORITY=1111111111&IsCtmLevle=1&UserName=jiusheng_ying%40163.com&IsStandard=0&LoginTime=06%2f27%2f2017+13%3a52%3a05&ExpireTime=06%2f27%2f2017+14%3a02%3a05&CtmAuthen=0000011000000001000111010000000011100011&BIsAgreed=true&IsResetPwd=0&CtmLiscense=1&AccessKey=ba44db5185b407af; AccessKey=55917cc17195442; RememberLoginInfo=member_name=EDAF729AC13010B65A91BA926668A4EB&user_name=DC3C6FD876242030296FF124B8C369E13EB30EB4F7E7530E; KWD=EMP=; LangType=Lang=&Flag=1';
        $webInfo = $this->getHtml($url,$cookie);
        //报错在跳转时直接去跳转
        if(strpos($webInfo, 'Object moved to ') !== false){
            preg_match('/<a href="(.*?)">here<\/a>/is',$webInfo,$match);
            $redirect_url = 'http://ehire.51job.com'.$match[1];
            redirect($redirect_url);
         }else{
            print_r($webInfo);
        }
    }

    /**
     * post获取Url内容
     * User: jsying@iflytek.com
     */
    private function postHtml($url,$data,$cookie=''){
        $data = is_array($data) ? http_build_query($data, null, "&") : $data;
        $ch = curl_init ();
        curl_setopt ( $ch, CURLOPT_URL, $url ); // 获取传输URL
        curl_setopt ( $ch, CURLOPT_RETURNTRANSFER, 1 ); // 结果数据只返回、不输出
        curl_setopt ( $ch, CURLOPT_ENCODING, 'gzip, deflate, sdch');//header中“Accept-Encoding: ”部分的内容，支持的编码格式为："identity"，"deflate"，"gzip"。如果设置为空字符串，则表示支持所有的编码格式
        curl_setopt ( $ch, CURLOPT_TIMEOUT, 50 ); // 设置响应秒数
        curl_setopt($ch, CURLOPT_POST, true);
        curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
        if(!empty($cookie)){
            curl_setopt($ch,CURLOPT_COOKIE ,$cookie);
        }
        $this->setHeader();
        $webInfo = curl_exec ( $ch );
        if(curl_errno($ch))
        {
            $error = curl_error ($ch);
            print_r($error);
        }
        curl_close($ch);
        return $webInfo ;
    }

    /**
     * get获取Url内容
     * User: jsying@iflytek.com
     */
    private function getHtml($url,$cookie=''){
        $ch = curl_init ();
        curl_setopt ( $ch, CURLOPT_URL, $url ); // 获取传输URL
        curl_setopt ( $ch, CURLOPT_RETURNTRANSFER, 1 ); // 结果数据只返回、不输出
        curl_setopt ( $ch, CURLOPT_ENCODING, 'gzip, deflate, sdch');//header中“Accept-Encoding: ”部分的内容，支持的编码格式为："identity"，"deflate"，"gzip"。如果设置为空字符串，则表示支持所有的编码格式
        curl_setopt ( $ch, CURLOPT_TIMEOUT, 50 ); // 设置响应秒数
        if(!empty($cookie)){
            curl_setopt($ch,CURLOPT_COOKIE ,$cookie);
        }
        $this->setHeader();
        $webInfo = curl_exec ( $ch );
        if(curl_errno($ch))
        {
            $error = curl_error ($ch);
            print_r($error);
        }
        curl_close($ch);
        return $webInfo ;
    }

    /**
     * [setHeader]: 设置头部
     * User: jsying@iflytek.com
     */
    private function setHeader()
    {
        $header = array();
        $header[] = "User-Agent:Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.110 Safari/537.36";
        $header[] = "CLIENT-IP:{$this->useIp()}";
        $header[] = "X-FORWARDED-FOR:{$this->useIp()}";
        curl_setopt($this->curlHandle, CURLOPT_HTTPHEADER, $header);
    }

    /**
     * [useIp]: 伪造ip
     * @param null $ip
     * @return null|string
     * User: jsying@iflytek.com
     */
    public function useIp($ip = NULL)
    {
        if ($ip == NULL) {
            $ip1 = rand(10, 255);
            $ip2 = rand(10, 255);
            $ip3 = rand(10, 255);
            $ip4 = rand(10, 255);
            $ip = "{$ip1}.{$ip2}.{$ip3}.{$ip4}";
        }
        return  $ip;
    }
}
```


