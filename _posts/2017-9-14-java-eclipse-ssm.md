---
layout: post
title:  "Eclipse下 搭建maven项目,并集成ssm框架"
categories: Java ssm
tags:  Java ssm Eclipse
---

* content
{:toc}
需先本地安装完基本相关环境，方可进行接下来操作。搭建过程中，可能遇到未知错误或者问题，具体情况需要自行解决。

<!--excerpt-->
## 新建maven项目
- 确认Eclipse已经支持maven可创建maven项目
- 选择工作空间，下一步，选择webapp
- 再下一步，选择新建的项目名称
- 创建完之后需要配置项目基本的编码为utf-8
- 选择项目的jdk为1.8 （maven生成的为1.5）
- 选择项目的Dynamic Web Moduel为3.0（应该选择时会报错需要手动修改setting文件内容，并同时修改web.xml内。可见介绍[http://www.imooc.com/article/19402](http://www.imooc.com/article/19402)）
- 确认项目的maven库和jdk使用正确，使得项目在Tomcat下运行。
- 最后项目index.jsp仍有报错。需添加如下代码在pom.xml中

```
    <!-- 导入java ee jar 包 -->  
            <dependency>  
                <groupId>javax</groupId>  
                <artifactId>javaee-api</artifactId>  
                <version>7.0</version>  
            </dependency>  
```
其他问题解决：
1. 使用 maven-compiler-plugin 将 maven 编译级别改为 jdk1.6 以上：

```
    <build>  
        <plugins>  
            <!-- define the project compile level -->  
            <plugin>  
                <groupId>org.apache.maven.plugins</groupId>  
                <artifactId>maven-compiler-plugin</artifactId>  
                <version>2.3.2</version>  
                <configuration>  
                    <source>1.7</source>  
                    <target>1.7</target>  
                </configuration>  
            </plugin>  
        </plugins>  
    </build>  
```
参考[blog.csdn.net/defonds/article/details/47974269](blog.csdn.net/defonds/article/details/47974269)

### 参考文章
- [SSM框架——详细整合教程（Spring+SpringMVC+MyBatis）](http://blog.csdn.net/yipanbo/article/details/45644751)
- [SSM框架——详细整合教程（Spring+SpringMVC+MyBatis）](http://blog.csdn.net/zhshulin/article/details/37956105)
- [SSM框架Web程序的流程（Spring SpringMVC Mybatis）](http://www.linuxidc.com/Linux/2016-08/134273.htm)
- [小白用eclipse创建一个maven+web3.0+JDK1.7+tomcat7.0的web项目](http://m.blog.csdn.net/lxjzqj2007/article/details/60466255)
- [eclipse构建maven的web项目 ](http://blog.csdn.net/smilevt/article/details/8215558/)