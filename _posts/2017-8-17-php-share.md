---
layout: post
title:  "PHP分享插件"
categories: PHP
tags:  PHP
---

* content
{:toc}
PHP的分享插件

## 代码实现
html：

```
    <link rel="stylesheet" type="text/css" href="/css/share.css'">
    <script src="/js/share.js'"></script>
    <div id="socialShare" class="course-attr"></div>
    <srcipt>
    $(function() {
        $("#socialShare").socialShare({
            content: '1111',
            url:window.location.href,
            title:'111111',
            pic:""
        });
    });
    </script>
```
+两张图片

见网盘20178-17/php-share