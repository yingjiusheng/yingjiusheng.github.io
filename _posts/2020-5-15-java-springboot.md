---
layout: post
title:  "SpringBoot的学习（二）"
categories: java SpringBoot
tags: java
---

* content
{:toc}
记录springBoot框架的学习。

<!--excerpt-->

##  视图层

###  引入thymeleaf

```xml
<!--  thymeleaf前端模板引擎    -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

修改基本配置  

```xml
#开发时关闭缓存,不然没法看到实时页面
spring:
  thymeleaf:
    cache: false
```

### thymeleaf语法

页面头部需引用

```html
<html xmlns:th="http://www.thymeleaf.org">
```

[语法](https://www.jianshu.com/p/d1370aeb0881)

### 视图层资源引用

- 使用[webjar](https://www.webjars.org/) 里的layui

  ```xml
  <!--  layui的webjars引用  -->
  <dependency>
      <groupId>org.webjars</groupId>
      <artifactId>layui</artifactId>
      <version>2.5.5</version>
  </dependency>
  ```

  ```html
  <link rel="stylesheet" type="text/css" href="/backend/layui/css/layui.css" th:href="@{/webjars/layui/2.5.5/css/layui.css}"/>
  ```

## 视图层layout

###  公共模块抽取

```html
<div th:fragment="header" class="layui-header">
    <div class="layui-logo">layui 后台布局</div>
</div>
```

### 公共模块使用

- th:insert  插入

- th:replace 替换

- th:include 包含

  ```html
  <div th:replace="~{/backend/layout::header}"></div>
  ```

###  公用css页面抽取

```html
<head th:fragment="common_header(title,links)">
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">
    <title th:replace="${title}">layout 后台大布局 - Layui</title>
    <!-- Common styles and scripts -->
    <link rel="stylesheet" type="text/css" href="/backend/layui/css/layui.css" th:href="@{/webjars/layui/2.5.5/css/layui.css}"/>
    <th:block th:replace="${links}" />
</head>
```

###  公用css页面使用

```html
<head th:replace="backend/layout :: common_header(~{::title},~{::link})">
    <title>列表</title>
    <link rel="stylesheet" href="css/index22.css">
</head>
```

```html
<head th:replace="backend/layout :: common_header(~{::title},~{})">
    <title>首页</title>
</head>
```

[示例](https://www.jianshu.com/p/2102fa4772ba)

[官方介绍](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#template-layout)

## 错误定制

### 错误的html浏览器页面

- timestamp：时间戳 
- status：状态码 
- error：错误提示 
- exception：异常对象 
- message：异常消息 
- errors：JSR303数据校验的错误都在这里

Template下面没有error文件夹。那么，就会去静态资源文件夹下寻找error文件夹

以上都没有错误页面，就会默认来到SpringBoot默认的错误提示页面



### 错误的json接口数据定制

[简单介绍](https://blog.csdn.net/m0_38143867/article/details/93605683)

