---
layout: post
title:  "windows下laravel框架前端编译资源laravel-mix环境搭建"
categories: PHP Laravel
tags:   PHP Laravel Laravel-mix Webpack
---

* content
{:toc}
Laravel Mix 提供了一套流式 API，使用一些通用的 CSS 和 JavaScript 预处理器为 Laravel 应用定义 Webpack 构建步骤。但是在环境安装时，并非像文档中命令安装的那么顺利，其中就遇到了很多的问题。

<!--excerpt-->
# 简介
Laravel Mix 提供了一套流式 API，使用一些通用的 CSS 和 JavaScript 预处理器为 Laravel 应用定义 Webpack 构建步骤。此篇针对于环境的安装与搭建做出说明。部分内容可参见[[ Laravel 5.4 文档 ] 前端 —— 编译资源（Laravel Mix）](https://laravelacademy.org/post/6798.html)。
# 环境要求说明
此篇仅仅针对于windows下的命令的运行
- node（v6.11.2）
- npm（v5.4.0）
- Python2.7（Python3.*不支持）
- 其他
# 步骤说明
## 安装node
1. [node官网](https://nodejs.org/en/download/)
2. 直接下载.msi文件下载安装下一步
3. 添加全局变量
4. 验证：node -v 
## 安装npm
1. 安装node以后自动会安装NPM，*由于新版的NodeJS已经集成了npm，所以之前npm也一并安装好了*。
2. 验证npm -v
3. 如果想要升级，npm install -g npm
## 安装Python2.7
1. [Python官网](https://www.python.org/getit/)
2. 下载Python2.7文件，window下可直接运行
3. 添加至全局变量
## 运行instal测试
如果你正在 Windows 系统上开发，需要在运行 npm install 命令时带上 --no-bin-links：

```
npm install --no-bin-links
```
此过程运行时间很长，在项目根目录下回出现node_modules文件夹

运行过程中可能会出现以下问题：
- MSBUILD : error MSB3428: 未能加载 Visual C++ 组件“VCBuild.exe”。
要解决此问题，安装 .NET Fram ework 2.0 SDK；2) 安装 Microsoft Visual Studio 2005；或 3) 如果将该组件安装到了 其他位置，请将其位置添加到系统 路径中。 
- 解决方法：
 
1.管理员安装相关Windows下的[工具](https://github.com/nodejs/node-gyp)，执行命令如下：
```
npm install --global --production windows-build-tools
```
2. 安装.NET Fram ework 2.0 SDK；添加至系统变量(此处360软件管家里搜索便可安装)*此处暂时不知道是否是必要的操作，优先进行上一个方法。*
3. [npm install 无响应解决方案](http://www.cnblogs.com/Imever/p/6053932.html)

## 安装完成后
### 运行 Mix

```
// 运行所有 Mix 任务...
npm run dev

// 运行所有 Mix 任务并减少输出...
npm run production
```

可能会出现如下错误

```
'NODE_ENV' 不是内部或外部命令，也不是可运行的程序
或批处理文件。

npm ERR! Windows_NT 6.1.7601
npm ERR! argv "D:\\nodejs\\node.exe" "D:\\nodejs\\node_modules\\npm\\bin\\npm-cli.js" "start"
npm ERR! node v4.0.0-rc.5
npm ERR! npm  v2.14.2
npm ERR! code ELIFECYCLE
npm ERR! yy-ydh-web@1.0.7 start: `npm run clear&& NODE_ENV=development && webpack-dev-server --host 0.0.0.0 --devtool ev
al --progress --color --profile`
npm ERR! Exit status 1
npm ERR!
npm ERR! Failed at the yy-ydh-web@1.0.7 start script 'npm run clear&& NODE_ENV=development && webpack-dev-server --host
0.0.0.0 --devtool eval --progress --color --profile'.
npm ERR! This is most likely a problem with the yy-ydh-web package,
npm ERR! not with npm itself.
npm ERR! Tell the author that this fails on your system:
npm ERR!     npm run clear&& NODE_ENV=development && webpack-dev-server --host 0.0.0.0 --devtool eval --progress --color
 --profile
npm ERR! You can get their info via:
npm ERR!     npm owner ls yy-ydh-web
npm ERR! There is likely additional logging output above.

npm ERR! Please include the following file with any support request:
npm ERR!     D:\workspace\node_modules\yy-ydh-web\npm-debug.log
```
简单来说，就是windows不支持NODE_ENV=development的设置方式。

### 解决方法：
- 安装cross-env:

```
npm install cross-env --save-dev
```
运行时可能好像还是有问题，但后来在目录下面执行

cnpm install 就安装了一些东西，不知道怎么就成功，然后就能跑起来了