---
layout: post
title:  "nginx-编写简单http模块（hello world）"
categories: nginx
tags:  nginx
---


* content
{:toc}

写一个Nginx的http模块，当向Nginx发送请求时，Nginx返回“Hello World!”


<!--excerpt-->

## 代码
	文件名：ngx_http_mytest_module.c

```go

#include <ngx_config.h>
#include <ngx_core.h>
#include <ngx_event.h>
#include <ngx_http.h>

static ngx_int_t ngx_http_mytest_handler(ngx_http_request_t *r)
{
	if (!(r->method & (NGX_HTTP_GET|NGX_HTTP_HEAD)))
		return NGX_HTTP_NOT_ALLOWED;

	ngx_int_t rc = ngx_http_discard_request_body(r);
	if (rc != NGX_OK)
			return rc;

	ngx_str_t type = ngx_string("text/plain");
	ngx_str_t response = ngx_string("Hello World!");

	r->headers_out.status = NGX_HTTP_OK;
	r->headers_out.content_length_n = response.len;
	r->headers_out.content_type = type;

	rc = ngx_http_send_header(r);
	if (rc == NGX_ERROR || rc > NGX_OK || r->header_only)
			return rc;

	ngx_buf_t *b = ngx_create_temp_buf(r->pool, response.len);
	if (b == NULL)
		return NGX_HTTP_INTERNAL_SERVER_ERROR;

	ngx_memcpy(b->pos, response.data, response.len);
	b->last = b->pos + response.len;
	b->last_buf = 1;

	ngx_chain_t out;
	out.buf = b;
	out.next = NULL;

	return ngx_http_output_filter(r, &out);
}

static char* ngx_http_mytest(ngx_conf_t *cf, ngx_command_t *cmd, void *conf)
{
	ngx_http_core_loc_conf_t *clcf;

	clcf = ngx_http_conf_get_module_loc_conf(cf, ngx_http_core_module);

	clcf->handler = ngx_http_mytest_handler;
	
	return NGX_CONF_OK;
}

static ngx_command_t ngx_http_mytest_commands[] = {
	{
		ngx_string("mytest"),
		NGX_HTTP_MAIN_CONF | NGX_HTTP_SRV_CONF | NGX_HTTP_LOC_CONF | NGX_HTTP_LMT_CONF | NGX_CONF_NOARGS,
		ngx_http_mytest,
		NGX_HTTP_LOC_CONF_OFFSET,
		0,
		NULL
	},
	ngx_null_command
};

static ngx_http_module_t ngx_http_mytest_module_ctx = {
	NULL,
	NULL,
	NULL,
	NULL,
	NULL,
	NULL,
	NULL,
	NULL,	
};

ngx_module_t ngx_http_mytest_module = {
	NGX_MODULE_V1,
	&ngx_http_mytest_module_ctx,
	ngx_http_mytest_commands,
	NGX_HTTP_MODULE,
	NULL,
	NULL,
	NULL,
	NULL,
	NULL,
	NULL,
	NULL,
	NGX_MODULE_V1_PADDING
};

```

## 将代码编译进nginx

1.将代码放到Nginx源码目录下
        nginx源码目录为：/opt/nginx-1.5.4
        在/opt/nginx-1.5.4/src下新建目录mytest，将代码放到该目录下。
2.在mytest目录下编写文件config：

```go

ngx_addon_name=ngx_http_mytest_module
HTTP_MODULES="$HTTP_MODULES ngx_http_mytest_module"
NGX_ADDON_SRCS="$NGX_ADDON_SRCS $ngx_addon_dir/ngx_http_mytest_module.c"

```

3.configure时添加自定义模块的路径：
       `./configure --add-module=/opt/nginx-1.5.4/src/mytest`

4.编译安装nginx

```go

   make
   make install

```

## 运行nginx

1.修改配置文件

在目录 `usr/local/nginx/conf/nginx.conf`修改：

![](http://img0.tuicool.com/qeEbEn.png)

## 结果

用浏览器访问Nginx：直接输入部署Nginx的机器IP地址
![](http://img0.tuicool.com/Z7ZF7vN.jpg!web)


原文地址：[推酷](http://www.tuicool.com/articles/aAR3uu)