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

class SpiderController extends ScriptController
{
    private $siteUrl = 'http://ehire.51job.com';
    private $cookie = 'guid=14884341582773240050; EhireGuid=c5b2e0c128784aacb78637339e418ac5; search=jobarea%7E%60000000%7C%21ord_field%7E%600%7C%21recentSearch0%7E%601%A1%FB%A1%FA000000%2C00%A1%FB%A1%FA000000%A1%FB%A1%FA0000%A1%FB%A1%FA00%A1%FB%A1%FA9%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FAPHp%A1%FB%A1%FA2%A1%FB%A1%FA%A1%FB%A1%FA-1%A1%FB%A1%FA1498113338%A1%FB%A1%FA0%A1%FB%A1%FA%A1%FB%A1%FA%7C%21recentSearch1%7E%601%A1%FB%A1%FA000000%2C00%A1%FB%A1%FA000000%A1%FB%A1%FA0000%A1%FB%A1%FA00%A1%FB%A1%FA2%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA%A1%FB%A1%FA2%A1%FB%A1%FA%A1%FB%A1%FA-1%A1%FB%A1%FA1498111889%A1%FB%A1%FA0%A1%FB%A1%FA%A1%FB%A1%FA%7C%21recentSearch2%7E%601%A1%FB%A1%FA000000%2C00%A1%FB%A1%FA000000%A1%FB%A1%FA0000%A1%FB%A1%FA00%A1%FB%A1%FA9%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA%A1%FB%A1%FA2%A1%FB%A1%FA%A1%FB%A1%FA-1%A1%FB%A1%FA1497922825%A1%FB%A1%FA0%A1%FB%A1%FA%A1%FB%A1%FA%7C%21recentSearch3%7E%601%A1%FB%A1%FA000000%2C00%A1%FB%A1%FA000000%A1%FB%A1%FA0000%A1%FB%A1%FA00%A1%FB%A1%FA9%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA01%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA%A1%FB%A1%FA2%A1%FB%A1%FA%A1%FB%A1%FA-1%A1%FB%A1%FA1498098589%A1%FB%A1%FA0%A1%FB%A1%FA%A1%FB%A1%FA%7C%21recentSearch4%7E%601%A1%FB%A1%FA000000%2C00%A1%FB%A1%FA000000%A1%FB%A1%FA0000%A1%FB%A1%FA00%A1%FB%A1%FA9%A1%FB%A1%FA99%A1%FB%A1%FA01%A1%FB%A1%FA01%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA99%A1%FB%A1%FA%A1%FB%A1%FA2%A1%FB%A1%FA%A1%FB%A1%FA-1%A1%FB%A1%FA1498098584%A1%FB%A1%FA0%A1%FB%A1%FA%A1%FB%A1%FA%7C%21collapse_expansion%7E%601%7C%21; nsearch=jobarea%3D%26%7C%26ord_field%3D%26%7C%26recentSearch0%3D%26%7C%26recentSearch1%3D%26%7C%26recentSearch2%3D%26%7C%26recentSearch3%3D%26%7C%26recentSearch4%3D%26%7C%26collapse_expansion%3D; _ujz=ODczNTIxMDgw; ps=us%3DWWIFaAN%252FUGMFel8yBmZcagEsV2dbbVI3XSdTMFtlAzsLOgBtD2tUZlY%252BD2ZVMVRhAjFQZlAqU35RFgEBDHRVDA%253D%253D%26%7C%26needv%3D0; slife=resumeguide%3D1%26%7C%26indexguide%3D1%26%7C%26loginwarning%3D1%26%7C%26lowbrowser%3Dnot%26%7C%26lastlogindate%3D20170627; 51job=cenglish%3D0; ASP.NET_SessionId=fiyp1jfstz2ykg4fgn5vx4oy; HRUSERINFO=CtmID=2934808&DBID=2&MType=02&HRUID=3383275&UserAUTHORITY=1111111111&IsCtmLevle=1&UserName=linwan101&IsStandard=0&LoginTime=06%2f27%2f2017+17%3a51%3a32&ExpireTime=06%2f27%2f2017+18%3a01%3a32&CtmAuthen=0000011000000001000110010000000011100001&BIsAgreed=true&IsResetPwd=0&CtmLiscense=6&AccessKey=6844f466d937b616; AccessKey=abb3d95057d24fc; RememberLoginInfo=member_name=EDAF729AC13010B6FDEACBE11FBB87CE&user_name=F475C9DBA33D8FCA1A0DCE39DB63B592; LangType=Lang=&Flag=1';
    public function _initialize()
    {
        parent::_initialize();
    }

    /**
     * [index]:爬虫入口
     * User: jsying@iflytek.com
     */
    function index()
    {
        $url = $this->siteUrl.'/Candidate/SearchResumeNew.aspx';
        $cookie = $this->cookie;
        $data = array();
        /* 仅仅获取到的是第一个单页的数据，分页post */
        $webInfo = $this->postHtml($url, $data, $cookie);
        if (strpos($webInfo, 'Object moved to ') !== false) {
            preg_match('/<a href="(.*?)">here<\/a>/is', $webInfo, $match);
            $redirect_url = $this->siteUrl . $match[1];
            echo '<br>没有获取到内容，跳转链接为：' . $redirect_url;
        } else {
            $filePath = $this->getFilePath();
            if(!empty($filePath)) file_put_contents($filePath,$webInfo);
            $webInfo = file_get_contents($filePath);
            echo 'dd';
            print_r($webInfo);
            exit;
        }
    }

    /**
     * [getHrefByListHtml]: 列表页面获取所有的简历a链接
     * User: jsying@iflytek.com
     */
    public function getHrefByListHtml(){
        $webInfo = file_get_contents('./data/temp/content/2017-06-27/1.txt');
        $list = array();
        preg_match('/<div class="Common_list-table">.*?<table width="100%" border="1">.*?<tbody>(.*?)<\/tbody>.*?<\/table>.*?<\/div>/is',$webInfo,$box);
        preg_match_all('/<span id=".*?"><a href="(.*?)".*?<\/span>/is',$box[1],$match);
        if(!empty($match[1])){
            foreach($match[1] as $val){
                $list[] = $this->siteUrl.$val;
            }
        }
        print_r($list);
        return $list;
    }

    /**
     * [getContentByHref]: 获取简历链接的内容
     * User: jsying@iflytek.com
     */
    public function getContentByHref()
    {
        $href = 'http://ehire.51job.com/Candidate/ResumeView.aspx?hidUserID=952878151&hidEvents=23&pageCode=3&hidKey=0cd8f143d51757c6c923417fd581a4e7';
        $info = $this->getHtml($href,$this->cookie);
        print_r($info);
    }

    /**
     * [getFilePath]:创建文件夹
     * @param string $filename
     * @param string $savePath
     * @return bool|string
     * User: jsying@iflytek.com
     */
    private function getFilePath($filename='',$savePath = ''){
        /* 创建文件夹 */
        if(empty($savePath)) $savePath = './data/temp/content/'.date('Y-m-d');
        if(empty($filename)) $filename = date('H_i_s').'.txt';
        if(!is_dir($savePath)) {
            // 尝试创建目录
            if(!mkdir($savePath,0777,true)){
                $this->error  =  '上传目录'.$savePath.'不存在';
                return false;
            }
        }else {
            if(!is_writeable($savePath)) {
                $this->error  =  '上传目录'.$savePath.'不可写';
                return false;
            }
        }
        return $savePath.'/'.$filename;
    }

    /**
     * post获取Url内容
     * User: jsying@iflytek.com
     */
    private function postHtml($url, $data, $cookie = '')
    {
        $data = is_array($data) ? http_build_query($data, null, "&") : $data;
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url); // 获取传输URL
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1); // 结果数据只返回、不输出
        curl_setopt($ch, CURLOPT_ENCODING, 'gzip, deflate');//header中“Accept-Encoding: ”部分的内容，支持的编码格式为："identity"，"deflate"，"gzip"。如果设置为空字符串，则表示支持所有的编码格式
        curl_setopt($ch, CURLOPT_TIMEOUT, 50); // 设置响应秒数
        curl_setopt($ch, CURLOPT_POST, true);
        curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
        if (!empty($cookie)) {
            curl_setopt($ch, CURLOPT_COOKIE, $cookie);
        }
        $this->setHeader();
        $webInfo = curl_exec($ch);
        if (curl_errno($ch)) {
            $error = curl_error($ch);
            print_r($error);
        }
        curl_close($ch);
        return $webInfo;
    }

    /**
     * get获取Url内容
     * User: jsying@iflytek.com
     */
    private function getHtml($url, $cookie = '')
    {
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url); // 获取传输URL
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1); // 结果数据只返回、不输出
        curl_setopt($ch, CURLOPT_ENCODING, 'gzip, deflate, sdch');//header中“Accept-Encoding: ”部分的内容，支持的编码格式为："identity"，"deflate"，"gzip"。如果设置为空字符串，则表示支持所有的编码格式
        curl_setopt($ch, CURLOPT_TIMEOUT, 50); // 设置响应秒数
        if (!empty($cookie)) {
            curl_setopt($ch, CURLOPT_COOKIE, $cookie);
        }
        $this->setHeader();
        $webInfo = curl_exec($ch);
        if (curl_errno($ch)) {
            $error = curl_error($ch);
            print_r($error);
        }
        curl_close($ch);
        return $webInfo;
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
    private function useIp($ip = NULL)
    {
        if ($ip == NULL) {
            $ip1 = rand(10, 255);
            $ip2 = rand(10, 255);
            $ip3 = rand(10, 255);
            $ip4 = rand(10, 255);
            $ip = "{$ip1}.{$ip2}.{$ip3}.{$ip4}";
        }
        return $ip;
    }
}
```


