---
layout: post
title:  "如何在项目中使用富文本编辑器UEditor"
categories: JavaScript
tags:  JavaScript PHP UEditor
---

* content
{:toc}

[UEditor](http://ueditor.baidu.com/website/)UEditor是由百度web前端研发部开发所见即所得富文本web编辑器，具有轻量，可定制，注重用户体验等特点，开源基于MIT协议，允许自由使用和修改代码；
<!--excerpt-->

## 使用方法
编辑器主要的方法都在[demo](http://ueditor.baidu.com/website/onlinedemo.html);只要在项目中导入UEditor类，按照demo中方法就可以用了。
我们使用富文本编辑器主要就两种方式：写入和读取

  1.写入内容

```js
  function setContent(isAppendTo) {
        var arr = [];
        arr.push("使用editor.setContent('欢迎使用ueditor')方法可以设置编辑器的内容");
        UE.getEditor('editor').setContent('欢迎使用ueditor', isAppendTo);
        alert(arr.join("\n"));
    }
```
  2.读取内容

```js
 function getContent() {
        var arr = [];
        arr.push("使用editor.getContent()方法可以获得编辑器的内容");
        arr.push("内容为：");
        arr.push(UE.getEditor('editor').getContent());
        alert(arr.join("\n"));
    }
```

## 遇到的问题
开发中一个需求，需要在界面加载就显示文本内容，这时候用普通的setContent（）函数就一直没有效果，网页提示：Cannot set property 'innerHTML' of null ；
这主要是由于：不能创建editor之后马上使用ueditor.setContent('文本内容');
要等到创建完成之后才可以使用
这时候我们需要将写入代码改为：

```js
    UE.getEditor('container').addListener("ready", function () {
            // editor准备好之后才可以使用
            UE.getEditor('container').setContent('abc');

        });
```
