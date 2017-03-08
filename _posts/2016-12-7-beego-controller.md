---
layout: post
title:  "beego-控制器介绍"
categories: go
tags:  go beego
---


* content
{:toc}

基于beego的Controller的设计，我们只需要继承beego.Controller就可以了。

<!--excerpt-->

## beego.Controller的实现

beego.Controller实现了beego.ControllerInterface,beego.ControllerInterface定义了如下函数

```go


- Init(ct *context.Context, childName string, app interface{})

这个函数主要初始化了 Context、相应的 Controller 名称，模板名，初始化模板参数的容器 Data，app即为当前执行的Controller的reflecttype，这个app可以用来执行子类的方法。

- Prepare()

这个函数主要是为了用户扩展用的，这个函数会在下面定义的这些 Method 方法之前执行，用户可以重写这个函数实现类似用户验证之类。

- Get()

如果用户请求的 HTTP Method 是 GET，那么就执行该函数，默认是 403，用户继承的子 struct 中可以实现了该方法以处理 Get 请求。

- Post()

如果用户请求的 HTTP Method 是 POST，那么就执行该函数，默认是 403，用户继承的子 struct 中可以实现了该方法以处理 Post 请求。

- Delete()

如果用户请求的 HTTP Method 是 DELETE，那么就执行该函数，默认是 403，用户继承的子 struct 中可以实现了该方法以处理 Delete 请求。

- Put()

如果用户请求的 HTTP Method 是 PUT，那么就执行该函数，默认是 403，用户继承的子 struct 中可以实现了该方法以处理 Put 请求.

- Head()

如果用户请求的 HTTP Method 是 HEAD，那么就执行该函数，默认是 403，用户继承的子 struct 中可以实现了该方法以处理 Head 请求。

- Patch()

如果用户请求的 HTTP Method 是 PATCH，那么就执行该函数，默认是 403，用户继承的子 struct 中可以实现了该方法以处理 Patch 请求.

- Options()

如果用户请求的HTTP Method是OPTIONS，那么就执行该函数，默认是 403，用户继承的子 struct 中可以实现了该方法以处理 Options 请求。

- Finish()

这个函数是在执行完相应的 HTTP Method 方法之后执行的，默认是空，用户可以在子 struct 中重写这个函数，执行例如数据库关闭，清理数据之类的工作。

- Render() error

这个函数主要用来实现渲染模板，如果 beego.AutoRender 为 true 的情况下才会执行。

```


在这里，我们项目中常用的可能就是Prepare()，Finish()，Render()。

- Prepare()

这个函数和php各大框架的beforeAction是一个意思，即method执行前执行这个函数。我们可以重写这个函数已实现用户验证等action前期处理函数。

- Finish()

和prepare对应的是，这个函数事在method执行后执行，当我们需要在方法执行后处理一些东西，如清理内存等。就可以重写此函数。

- Render()

渲染模版，做web应用的时候会用到，需要设置 beego.AutoRender 为 true 的情况下才会执行。做api应用的时候不需要使用此函数。






