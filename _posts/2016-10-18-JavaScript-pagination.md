---
layout: post
title:  "自己写分页插件（支持ajax分页请求）"
categories: JavaScript
tags:  JavaScript 分页 ajax
---

* content
{:toc}

<!--excerpt-->

## 问题描述
分页是我们在几乎所有web项目中都会遇到的问题，网上的插件也是非常多，但是大部分满足不了我们的需求，网上大部分分页的原理是首先加载全部的数据，存入内存。然后直接从内存里面取数据，当我们数据量特别大的时候，这种方法就很难满足需求。下面是我自己写的一个初期的分页，很粗糙。后期慢慢改。
## 具体实现细节

1. css代码

```css
.cu_page{
	overflow: hidden;
}
.cu_page ul li{
	float: left;
	overflow: hidden;
	margin-right: 3px;
	font-size: 14px;
	color:#595959;
	line-height: 22px;
}
.cu_page ul li a{
	display: block;
	width:22px;
	height: 22px;
	line-height: 20px;
	text-align: center;
	color:#454545;
	background: #ebebeb;
	border:1px solid #dbdbdb;
	font-size: 11px;
	-webkit-text-size-adjust:none;
}
.cu_page ul li a.cuur,.cu_page ul li a:hover{
	color:#fff;
	background: #39a3e6;
	border:1px solid #288dd4;
}
.cu_page ul li a.cu_prev,.cu_page ul li a.cu_prev:hover,.cu_page ul li a.cu_next,.cu_page ul li a.cu_next:hover{
	width:18px;
	border:none;
	background: transparent;
	position: relative;
}

.cu_page ul li a.cu_prev:before{
	position: absolute;
	content: '';
	top:3px;
	left: -8px;
	border: 8px solid transparent;
    border-right-color: #a5a5a5;
}
.cu_page ul li a.cu_prev:after{
	position: absolute;
	content: '';
	top:3px;
	left:-7px;
	border: 8px solid transparent;
    border-right-color: #fff;
}
.cu_page ul li a.cu_next:before{
	position: absolute;
	content: '';
	top:3px;
	right: -8px;
	border: 8px solid transparent;
    border-left-color: #a5a5a5;
}
.cu_page ul li a.cu_next:after{
	position: absolute;
	content: '';
	top:3px;
	right:-7px;
	border: 8px solid transparent;
    border-left-color: #fff;
}
.cu_page ul li a.cu_next{
	margin-right: 5px;
}
.cu_page ul li span.cu_spct{
	display: block;
	width:22px;
	height: 22px;
	text-align: center;
	line-height: 14px;
	color:#9a9a9a;
}
.cu_page ul li.cu_palast{
	overflow: hidden;
	margin-left:10px;
}
.cu_page ul li.cu_palast span{
	float: left;
	font-size: 14px;
	color:#595959;
}
.cu_page ul li.cu_palast input[type=text]{
	float: left;
	width:22px;
	height: 22px;
	border:1px solid #dbdbdb;
	text-align: center;
	font-size: 9px;
	-webkit-text-size-adjust:none;
	margin:0 6px;
}
.cu_page ul li.cu_palast a{
	float: left;
	margin-left:10px;
	width:56px;
	text-align: center;
}
```

2.js代码


```js

var global_cur_page = 1;
var global_url = "";
var global_method = "get";
var global_func = "";

function create_page(cur_page,total_page,url,method,id,func){
    /*
     * cur_page 当前页
     * total_page 总共多少页
     * url 请求的url
     * method 请求方法
     * id 分页div的id，只有当页面中同时含有几个页面时，才会用到
     *func
     * */
    global_cur_page = cur_page;
    global_url = url;
    global_method = method;
    global_func = func;
    if(id == null)
        $(".cu_page").empty();
    else
        $("#" + id).empty();
    if(total_page <= 6){
        var page_header = "<ul> <li><a class='cu_prev' href='javascript:"+"on_pre_click();'></a></li>";
        var page_footer =   "<li><a class='cu_next'  href='javascript:"+"on_next_click("+ total_page+");'></a></li>" +
            " <li>共" + total_page +"页</li><li class='cu_palast'> <span>跳转到第</span> <input type='text' id='skip' value=''> <span>页</span>" +
            " <a href='javascript:on_skip_olick("+ total_page+");'>跳转</a> </li> </ul>";
        var page_body = "";

        for(var i = 1; i <= total_page; i++){
            if(i == cur_page){
                page_body += "<li><a class='cuur' id='"+ "page_" + i +"' href='javascript:on_page_click("+i+");'>" + i +"</a></li>"
            }else {
                page_body += "<li><a id='"+ "page_" + i +"' href='javascript:on_page_click("+i+");'>" + i +" </a></li>";
            }
        }
    }
    else if(total_page > 6 && (cur_page) < 5){
        var page_header = "<ul> <li><a class='cu_prev' href='javascript:"+"on_pre_click();'></a></li>";
        var page_footer =   "<li><span class='cu_spct'>...</span></li>" +
            "<li><a id='"+ "page_" + total_page +"' href='javascript:on_page_click("+total_page+");'>" + total_page +" </a></li>"
            +"<li><a class='cu_next' href='javascript:"+"on_next_click("+ total_page+");'></a></li>" +
            " <li>共" + total_page +"页</li><li class='cu_palast'> <span>跳转到第</span> <input type='text' id='skip' value=''> <span>页</span>" +
            " <a href='javascript:on_skip_olick("+ total_page+");'>跳转</a> </li> </ul>";
        var page_body = "";

        for(var i = 1; i <= 5; i++){
            if(i == cur_page){
                page_body += "<li><a id='"+ "page_" + i +"' class='cuur'  href='javascript:on_page_click("+i+");'>" + i +"</a></li>"
            }else {
                page_body += "<li><a id='"+ "page_" + i +"'  href='javascript:on_page_click("+i+");'>" + i +" </a></li>";
            }
        }
    }
    else if(total_page > 6 && (cur_page  > 5) && (total_page - cur_page) < 5){

        var page_header = "<ul> <li><a class='cu_prev'href='javascript:"+"on_pre_click();'></a></li>"
            +"<li><a  id='"+ "page_" + 1 +"' href='javascript:on_page_click(1);'>1</a></li>" +
            "<li><span class='cu_spct'>...</span></li>" ;

        var page_footer =   "<li><a class='cu_next' href='javascript:"+"on_next_click("+ total_page+");'></a></li>" +
            " <li>共" + total_page +"页</li><li class='cu_palast'> <span>跳转到第</span> <input type='text' id='skip' value=''> <span>页</span>" +
            " <a href='javascript:on_skip_olick("+ total_page+");'>跳转</a> </li> </ul>";
        var page_body = "";

        for(var i = total_page - 4; i <= total_page; i++){
            if(i == cur_page){
                page_body += "<li><a  id='"+ "page_" + i +"' class='cuur'  href='javascript:on_page_click("+i+");'>" + i +"</a></li>"
            }else {
                page_body += "<li><a  id='"+ "page_" + i +"' href='javascript:on_page_click("+i+");'>" + i +" </a></li>";
            }
        }
    }
    else{

        var page_header = "<ul> <li><a class='cu_prev' href='javascript:"+"on_pre_click();'></a></li>"
            +"<li><a  id='"+ "page_" + 1 +"'  href='javascript:on_page_click(1);'>1</a></li>" +
            "<li><span class='cu_spct'>...</span></li>";
        var page_footer =   "<li><span class='cu_spct'>...</span></li>" +
            "<li><a  id='"+ "page_" + total_page +"'  href='javascript:on_page_click("+total_page+");'>" + total_page +" </a></li>"
            +"<li><a class='cu_next' href='javascript:"+"on_next_click("+ total_page+");'></a></li>" +
            " <li>共" + total_page +"页</li><li class='cu_palast'> <span>跳转到第</span> <input type='text' id='skip' value=''> <span>页</span>" +
            " <a href='javascript:on_skip_olick("+ total_page+");'>跳转</a> </li> </ul>";
        var page_body = "";

        for(var i = cur_page-2; i <= cur_page +1; i++){
            if(i == cur_page){
                page_body += "<li><a  id='"+ "page_" + i +"' class='cuur'  href='javascript:on_page_click("+i+");'> " + i +"</a></li>"
            }else {
                page_body += "<li><a  id='"+ "page_" + i +"'  href='javascript:on_page_click("+i+");'>" + i +" </a></li>";
            }
        }
    }
    if(id == null)
        $(".cu_page").append(page_header + page_body + page_footer);
    else
        $("#" + id).append(page_header + page_body + page_footer);
}

//点击指定页
function on_page_click(page){
    $("#" + "page_" + global_cur_page).removeClass();
    $("#" + "page_" + page).addClass('cuur');
    global_cur_page = page;
    var point = (global_cur_page - 1)*10;
    if(global_method == "get"){
        window.location.href = global_url + "&point=" + point;
    }else{
        eval(global_func);
    }

}
//page表示当前页
function on_pre_click(){
    if(global_cur_page > 1){
        var pre_page = parseInt(global_cur_page) - 1;
        $("#" + "page_" + global_cur_page).removeClass();
        $("#" + "page_" + pre_page).addClass("cuur");
        global_cur_page = pre_page;
        var point = (global_cur_page - 1)*10;
        if(global_method == "get"){
            window.location.href = global_url + "&point=" + point;
        }else{
            eval(global_func);
        }
    }
}
//page表示当前页
function on_next_click(total_page){
    if(global_cur_page < total_page){
        var next_page = parseInt(global_cur_page) + 1;
        $("#" + "page_" + global_cur_page).removeClass();
        $("#" + "page_" + next_page).addClass("cuur");
        global_cur_page = next_page;
        var point = (global_cur_page - 1)*10;
        if(global_method == "get"){
            window.location.href = global_url + "&point=" + point;
        }else{
            eval(global_func);
        }
    }
}
//点击跳转页
function on_skip_olick(total_page){
    var skip_page = $("#skip").val();
    if(skip_page <= total_page && skip_page > 0){
        $("#" + "page_" + global_cur_page).removeClass();
        $("#" + "page_" + skip_page).addClass("cuur");
        global_cur_page = skip_page;
        var point = (global_cur_page - 1)*10;
        if(global_method == "get"){
            window.location.href = global_url + "&point=" + point;
        }else{
            eval(global_func);
        }
    }
}
```

3.效果图
分页的最终效果图如下：
 ![分页.jpg](https://raw.githubusercontent.com/liyoung1992/liyoung1992.github.io/master/_posts/%E5%88%86%E9%A1%B5.jpg)


