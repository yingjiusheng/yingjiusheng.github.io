---
layout: post
title:  "beego-路由设置"
categories: go
tags:  go beego
---


* content
{:toc}

beego存在三种方式的路由：固定路由、正则路由、自动路由

<!--excerpt-->

## 固定路由

固定路由也就是全匹配的路由，如：

```go

	beego.Router("/admin", &controllers.AdminController{})
	beego.Router("/list", &controllers.MainController{}, "*:List")
	beego.Router("/", &controllers.MainController{})
    beego.Router("/admin/index", &admin.ArticleController{})

```
这是我们最常用的路由形式，一个固定的路由，一个控制器，然后根据用户不同的请求来对应控制器的方法。和Yii里面controller，action一样的道理。

## 正则路由

平时不怎么用这个路由设置，看看beego文档的介绍：

```go

beego.Router(“/api/?:id”, &controllers.RController{})

默认匹配 //匹配 /api/123 :id = 123 可以匹配/api/这个URL
beego.Router(“/api/:id”, &controllers.RController{})

默认匹配 //匹配 /api/123 :id = 123 不可以匹配/api/这个URL
beego.Router(“/api/:id([0-9]+)“, &controllers.RController{})

自定义正则匹配 //匹配 /api/123 :id = 123
beego.Router(“/user/:username([\w]+)“, &controllers.RController{})

正则字符串匹配 //匹配 /user/astaxie :username = astaxie
beego.Router(“/download/*.*”, &controllers.RController{})

*匹配方式 //匹配 /download/file/api.xml :path= file/api :ext=xml
beego.Router(“/download/ceshi/*“, &controllers.RController{})

*全匹配方式 //匹配 /download/ceshi/file/api.json :splat=file/api.json
beego.Router(“/:id:int”, &controllers.RController{})

int 类型设置方式，匹配 :id为int类型，框架帮你实现了正则([0-9]+)
beego.Router(“/:hi:string”, &controllers.RController{})

string 类型设置方式，匹配 :hi为string类型。框架帮你实现了正则([\w]+)
beego.Router(“/cms_:id([0-9]+).html”, &controllers.CmsController{})

带有前缀的自定义正则 //匹配 :id为正则类型。匹配 cms_123.html这样的url :id = 123

```

## 自动路由
用户首先需要把需要路由的控制器注册到自动路由中：

    `beego.AutoRouter(&controllers.ObjectController{})`
那么 beego 就会通过反射获取该结构体中所有的实现方法，你就可以通过如下的方式访问到对应的方法中：

```go
    /object/login   调用 ObjectController 中的 Login 方法
    /object/logout  调用 ObjectController 中的 Logout 方法

```
除了前缀两个/:controller/:method的匹配之外，剩下的 url beego会帮你自动化解析为参数，保存在 this.Ctx.Input.Params 当中：

/object/blog/2013/09/12  调用 ObjectController 中的 Blog 方法，参数如下：map[0:2013 1:09 2:12]
方法名在内部是保存了用户设置的，例如 Login，url 匹配的时候都会转化为小写，所以，/object/LOGIN 这样的 url 也一样可以路由到用户定义的 Login 方法中。

现在已经可以通过自动识别出来下面类似的所有url，都会把请求分发到 controller 的 simple 方法：

```go

/controller/simple
/controller/simple.html
/controller/simple.json
/controller/simple.xml
```


