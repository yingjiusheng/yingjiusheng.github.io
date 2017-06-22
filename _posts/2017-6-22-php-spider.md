---
layout: post
title:  "curl伪登录51Job网站"
categories: PHP
tags:  PHP Spider
---

* content
{:toc}
php利用curl伪登录51job网站个人中心

<!--excerpt-->
## 说明
代码可直接通过index方法进入运行成功，登陆时生成一个cookiefile文件。在下次访问个人中心时会携带着cookiefile访问。个人中心登陆时没有涉及到验证码等问题，此代码仅为51Job上个人中心的伪登录，而非企业中心登陆。
## 控制器
直接上代码

```
namespace Script\Controller;
use Common\Controller\ScriptController;
use Script\Lib\Browser;

class SpiderController extends ScriptController{

    public function _initialize()
    {
        parent::_initialize();
        $this->browserIc = new Browser();
    }

    /**
     * [index]:爬虫入口
     * User: jsying@iflytek.com
     */
    function index(){
        $login_url = 'http://my.51job.com/my/My_Pmc.php';
        //登录
        $cookie_file = $this->login($login_url);
        if($cookie_file){
//            $next_url = 'http://i.51job.com/userset/my_51job.php?lang=c';//要访问的页面
            $next_url = 'http://search.51job.com/jobsearch/search_result.php?fromJs=1&keywordtype=2&lang=c&stype=2&postchannel=0000&fromType=1&confirmdate=9';
            $next_url = 'http://i.51job.com/resume/standard_resume.php?lang=c&resumeid=313930091';
            $web_info = $this->browserIc->visitByGet($next_url,$cookie_file);
            if(!empty($web_info)){
                $web_info = mb_convert_encoding ($web_info, "UTF-8", "gbk" );
                print_r($web_info);
                exit;
            }else{
                echo '登录后跳转页面抓取错误';
                return false;
            }
        }else{
            echo '模拟登录失败';
            return false;
        }
    }

    /**
     * 模拟登录
     */
    private function login($login_url){
        if(empty($login_url)) return false;
        //cookie保存文件地址
        $filename = parse_url($login_url, PHP_URL_HOST);
        $filename = trim($filename, "/") . ".tmp" .time(). rand(1000, 9999);
        $cookie_file = QSCMS_DATA_PATH . "temp/" . $filename;

        $ch=curl_init();
        $curlPost="username=jiusheng_ying@163.com&userpwd=yingjs0413&autologin=1";

        curl_setopt($ch,CURLOPT_URL,$login_url);
        curl_setopt($ch, CURLOPT_TIMEOUT,60);
        //启用时会将头文件的信息作为数据流输出
        curl_setopt($ch,CURLOPT_HEADER,0); //设定是否输出页面内容
        curl_setopt($ch,CURLOPT_RETURNTRANSFER,1);
        curl_setopt($ch,CURLOPT_POST,1);    //post,get 过去  CURLOPT_GET
        curl_setopt($ch,CURLOPT_POSTFIELDS,$curlPost);
        curl_setopt($ch,CURLOPT_COOKIEJAR,$cookie_file);    //保存cookie
        curl_exec($ch);

        if(curl_errno($ch))
        {
            $error = curl_error ($ch);
            print($error);
        }

        curl_close($ch);

        if(file_exists($cookie_file)){
            return $cookie_file;
        }else{
            return false;
        }
    }
}
```

## 类文件

```
<?php
namespace Script\Lib;
/**
 * 伪浏览器类
 */
class Browser
{
    private $curlHandle, $curHeader, $header;
    private $cookie, $userCookie, $userCookieLock, $proxy, $proxyPort;
    private $referer, $ip;

    public function __construct($url = NULL)
    {
        $this->initCurl();
        $this->cookie = NULL;
        $this->Referer = $url;
        $this->ip = NULL;
        $this->curHeader = NULL;
        $this->header[] = array();
    }


    /**
     * GET方式访问
     * @param string $url 访问地址
     * @param bool $isAjax 是否为ajax请求
     * @return string
     */
    public function visitByGet($url, $cookiefile='',$isAjax = false, $timeout = 60 )
    {
        if(empty($cookiefile)){
            $cookiefile = $this->getCookieFile($url);
        }
        curl_setopt($this->curlHandle, CURLOPT_COOKIEFILE, $cookiefile);
        curl_setopt($this->curlHandle, CURLOPT_COOKIEJAR, $cookiefile);
        curl_setopt($this->curlHandle, CURLOPT_TIMEOUT, $timeout);
        curl_setopt($this->curlHandle, CURLOPT_POST, false);
        curl_setopt($this->curlHandle, CURLOPT_URL, $url);

        $ssl = substr($url, 0, 8) == "https://" ? TRUE : FALSE;
        if ($ssl) {
            curl_setopt($this->curlHandle, CURLOPT_SSL_VERIFYHOST, FALSE);
            curl_setopt($this->curlHandle, CURLOPT_SSL_VERIFYPEER, false);
        }
        if ($this->proxy) {
            curl_setopt($this->curlHandle, CURLOPT_PROXY, $this->proxy);
            curl_setopt($this->curlHandle, CURLOPT_PROXYPORT, $this->proxyPort);
        }
        $this->setHeader($isAjax);
        $result = curl_exec($this->curlHandle);
        $lineList = explode("\n", $result);
        $contentList = array();
        $headerList = array();
        $headEnd = false;
        foreach ($lineList as $row) {
            if ($headEnd) {
                $contentList[] = $row;
            } else {
                $orow = $row;
                $row = trim($row);
                if (empty($row)) continue;
                $isHeader = preg_match("/^[0-9a-zA-Z\-_]{1,30}:/", $row);
                if (!$isHeader) {
                    strpos($row, "HTTP/") === 0 && $isHeader = true;
                }
                if ($isHeader) {
                    $headerList[] = $row;
                } else {
                    $headEnd = true;
                    $contentList[] = $orow;
                }
            }
        }
        $header = implode("\r\n", $headerList);
        $content = implode("\n", $contentList);
        $this->curHeader = $header;
        $this->referer = $url;
        return $content;
    }


    /**
     * POST方式访问
     * @param string $url 访问地址
     * @param array $data POST数组
     * @param bool $isAjax 是否为ajax请求
     */
    public function visitByPost($url, $data,$cookiefile='', $isAjax = false)
    {
        if(empty($cookiefile)){
            $cookiefile = $this->getCookieFile($url);
        }
        $data = is_array($data) ? http_build_query($data, null, "&") : $data;
        $this->setHeader($isAjax);
        curl_setopt($this->curlHandle, CURLOPT_COOKIEFILE, $cookiefile);
        curl_setopt($this->curlHandle, CURLOPT_COOKIEJAR, $cookiefile);
        curl_setopt($this->curlHandle, CURLOPT_POST, true);
        curl_setopt($this->curlHandle, CURLOPT_POSTFIELDS, $data);
        curl_setopt($this->curlHandle, CURLOPT_URL, $url);
        $result = curl_exec($this->curlHandle);
        $lineList = explode("\n", $result);
        $contentList = array();
        $headerList = array();
        $headEnd = false;
        foreach ($lineList as $row) {
            if ($headEnd) {
                $contentList[] = $row;
            } else {
                $orow = $row;
                $row = trim($row);
                if (empty($row)) continue;
                $isHeader = preg_match("/^[0-9a-zA-Z\-_]{1,30}:/", $row);
                if (!$isHeader) {
                    strpos($row, "HTTP/") === 0 && $isHeader = true;
                }
                if ($isHeader) {
                    $headerList[] = $row;
                } else {
                    $headEnd = true;
                    $contentList[] = $orow;
                }
            }
        }
        $header = implode("\r\n", $headerList);
        $content = implode("\n", $contentList);
        $this->curHeader = $header;
        $this->referer = $url;
        return $content;
    }


    /**
     * 下载文件
     * @param string $url
     * @param string $filename
     * @return int
     */
    public function download($url, $filename)
    {
        $content = $this->visitByGet($url);
        $writeNum = file_put_contents($filename, $content);
        return $writeNum;
    }

    /**
     * 伪造IP
     * @param string $ip IP地址
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
        $this->ip = $ip;
    }


    /**
     * 设置上一个访问页面地址
     */
    public function setReferer($url)
    {
        $this->referer = $url;
    }


    /**
     * 设置COOKIE
     */
    public function setCookie($cookiefile)
    {
        $this->userCookie = $cookiefile;
        $this->cookie = $cookiefile;
        $this->userCookieLock = $this->userCookie . ".lock";
        while (file_exists($this->userCookieLock)) {
            $statInfo = stat($this->userCookieLock);
            $time = $statInfo['mtime'];
            if (time() - $time > 30) break;
            usleep(300000);
        }
    }

    public function addHeader($info)
    {
        $this->header[] = $info;
    }

    /**
     * 获取当前的Header信息
     * @return array
     */
    public function getLastHeaderInfo()
    {
        $header = $this->curHeader;
        $headerLines = explode("\r\n", $header);
        $headerList = array();
        foreach ($headerLines as $line) {
            $pos = strpos($line, ":");
            if ($pos) {
                $key = substr($line, 0, $pos);
                $headerList[$key] = trim(substr($line, $pos + 1));
            } else {
                $headerList[] = $line;
            }
        }
        return $headerList;
    }


    /**
     * 获取当前HTTP状态
     */
    public function getLastHttpStatus()
    {
        $header = $this->curHeader;
        $headerLines = explode("\r\n", $header);
        $status = 500;
        foreach ($headerLines as $line) {
            $pos = strpos($line, "HTTP/");
            if ($pos !== false) {
                list($pro, $status) = explode(" ", $line);
                break;
            }
        }
        return (int)$status;
    }


    private function setHeader($isAjax)
    {
        $header = array();
        $header[] = "User-Agent:Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/33.0.1750.154";
        if ($this->ip) {
            $header[] = "CLIENT-IP:{$this->ip}";
            $header[] = "X-FORWARDED-FOR:{$this->ip}";
        }
        if ($this->referer) {
            $header[] = "Referer:{$this->referer}";
        }
        if ($isAjax) {
            $header[] = "X-Requested-With:XMLHttpRequest";
        }
        if ($this->header) {
            $header = array_merge($header, $this->header);
        }
        curl_setopt($this->curlHandle, CURLOPT_HTTPHEADER, $header);
    }

    /**
     * 设置代理
     * @param string $ip
     * @param string $port
     */
    public function setProxy($ip, $port = "80")
    {
        $this->proxy = $ip;
        $this->proxyPort = $port;
    }

    /**
     *设置超时
     */
    public function setTimeout($sec)
    {
        curl_setopt($this->curlHandle, CURLOPT_TIMEOUT, $sec);
        curl_setopt($this->curlHandle, CURLOPT_CONNECTTIMEOUT, $sec);
    }

    private function initCurl()
    {
        $this->curlHandle = curl_init();
        curl_setopt($this->curlHandle, CURLOPT_HEADER, 1);
        curl_setopt($this->curlHandle, CURLOPT_RETURNTRANSFER, 1);
        curl_setopt($this->curlHandle, CURLOPT_FOLLOWLOCATION, true);
    }

    private function getCookieFile($url)
    {
        if ($this->cookie == NULL) {
            $filename = parse_url($url, PHP_URL_HOST);
            $filename = trim($filename, "/") . ".tmp" . rand(1000, 9999);
            $filename = QSCMS_DATA_PATH . "temp/" . $filename;
            $this->cookie = $filename;
        }
        if ($this->userCookie) {
            file_put_contents($this->userCookieLock, time());
        }

        return $this->cookie;
    }

    public function __destruct()
    {
        curl_close($this->curlHandle);
        if ($this->cookie != $this->userCookie) {
            @unlink($this->cookie);
        } else {
            @unlink($this->userCookieLock);
        }
    }

    /**
     * 获取当前电脑操作系统
     */
    function getOS()
    {
        $OS = $_SERVER['HTTP_USER_AGENT'];
        if (preg_match('/win/i', $OS)) {
            $OS = 'Windows';
        } elseif (preg_match('/mac/i', $OS)) {
            $OS = 'MAC';
        } elseif (preg_match('/linux/i', $OS)) {
            $OS = 'Linux';
        } elseif (preg_match('/unix/i', $OS)) {
            $OS = 'Unix';
        } elseif (preg_match('/bsd/i', $OS)) {
            $OS = 'BSD';
        } elseif (preg_match('/iphone/i', $OS)) {
            $OS = 'iphone';
        } elseif (preg_match('/android/i', $OS)) {
            $OS = 'android';
        } else {
            $OS = '';
        }
        return $OS;
    }

    /**
     * 获取当前浏览器版本
     */
    function getbrowser()
    {
        $Agent = $_SERVER['HTTP_USER_AGENT'];
        $browseragent = "";   //浏览器
        $browserversion = ""; //浏览器的版本
        if (ereg('MSIE ([0-9].[0-9]{1,2})', $Agent, $version)) {
            $browserversion = $version[1];
            $browseragent = "Internet Explorer";
        } else if (ereg('Opera/([0-9]{1,2}.[0-9]{1,2})', $Agent, $version)) {
            $browserversion = $version[1];
            $browseragent = "Opera";
        } else if (ereg('Firefox/([0-9.]{1,5})', $Agent, $version)) {
            $browserversion = $version[1];
            $browseragent = "Firefox";
        } else if (ereg('Chrome/([0-9.]{1,3})', $Agent, $version)) {
            $browserversion = $version[1];
            $browseragent = "Chrome";
        } else if (ereg('Safari/([0-9.]{1,3})', $Agent, $version)) {
            $browseragent = "Safari";
        } else {
            $browseragent = " ";
        }
        return $browseragent;
    }

    public function getLastError()
    {
        return curl_errno($this->curlHandle);
    }

}

?>
```

## 运用此类的其余控制器案例


```
<?php
/**
 * 春雨问答采集
 * yjs
 */
set_time_limit(0);
class ChunyuAskCaijiApp extends BaseScriptApp{
	public $mod,$browserIc;
	public function __construct(){
		parent::__construct();
		$this->browserIc = &ic('browser');
	}
	
	function index() {
		$result = $this->login();
		$header = $result['header'];
		$response = $result['response'];
		if($header['Location']){
			print_r($response);
		}else{
			print_r('登录失败');
		}
	}
	/**
	 * 模拟登录
	 * yingjs
	 * @param unknown_type $url
	 * @return multitype:NULL
	 */
	function login($url){
		$result = array();
		$loginUrl = 'https://ssl.chunyuyisheng.com/ssl/api/weblogin/';
		$loginWeb = $this->browserIc->visitByGet($loginUrl);
		preg_match("/<input.*? name='csrfmiddlewaretoken' value='(.*?)' \/>/is", $loginWeb , $match);
		$url = 'http://chunyuyisheng.com/clinics';
		$data['username'] = '18602500579';
		$data['password'] = 'yingjs0413';
		$data['next'] = '/clinics';
		$data['csrfmiddlewaretoken'] = $match[1];
		$result['response'] = $this->browserIc->visitByPost($loginUrl,$data);
		$result['header'] = $this->browserIc->getLastHeaderInfo();
		return $result;
	}
}
```

