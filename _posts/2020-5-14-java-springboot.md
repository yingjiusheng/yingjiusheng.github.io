---
layout: post
title:  "SpringBoot的学习（一）"
categories: java SpringBoot
tags: java
---

* content
{:toc}
记录springBoot框架的学习。

<!--excerpt-->

##  IDEA搭建SpringBoot框架

###  仅安装web支持

![构建1](https://yingjiusheng.github.io/images/springboot/1.png)

![构建2](https://yingjiusheng.github.io/images/springboot/2.png)

![构建3](https://yingjiusheng.github.io/images/springboot/3.png)

###  自动生成maven等依赖

![构建4](https://yingjiusheng.github.io/images/springboot/4.png)

###  放入svn仓库

###  运行成功

![构建5](https://yingjiusheng.github.io/images/springboot/5.png)

##  修改基本配置

###  properties文件转为yml

方便使用

### 多环境配置

通过spring.profile.active=dev激活当前为哪个开发环境

可以编写类似于 application-dev(或prod).yml/properties文件。

###  yml的多文档编写

`````yml
# 开发环境配置
server:
  # 服务器的HTTP端口，默认为80
  port: 8080
spring:
  # 当前开发环境激活
  profiles:
#    前开发环境激活为 dev或prod模式
    active: dev

---
spring:
  profiles: dev
server:
  port: 8081
---

---
spring:
  profiles: prod
server:
  port: 8082
---
`````

##  新增开发热部署工具

###  添加dev-tools依赖

idea可以通过ctrl+F9 进行热刷新

```java
<!-- spring-boot-devtools -->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-devtools</artifactId>
   <optional>true</optional> <!-- 表示依赖不会传递 -->
</dependency>
```

##  日志的使用

```java
package com.example.demo1.project.backend.controller;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
@RequestMapping("/backend")
public class IndexController {

    /**
     * 首页
     */
    @GetMapping("/index")
    @ResponseBody
    public String index() {
        Logger logger = LoggerFactory.getLogger(IndexController.class);
        logger.info("这是日志");
        return "index";
    }
}
```

###  新增日志的logback.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!-- 日志存放路径 -->
   <property name="log.path" value="D:/Java/workSpaceForIDEA/demo1/log" />
    <!-- 日志输出格式 -->
   <property name="log.pattern" value="%d{HH:mm:ss.SSS} [%thread] %-5level %logger{20} - [%method,%line] - %msg%n" />

   <!-- 控制台输出 -->
   <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
      <encoder>
         <pattern>${log.pattern}</pattern>
      </encoder>
   </appender>

   <!-- 系统日志输出 -->
   <appender name="file_info" class="ch.qos.logback.core.rolling.RollingFileAppender">
       <file>${log.path}/sys-info.log</file>
        <!-- 循环政策：基于时间创建日志文件 -->
      <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 日志文件名格式 -->
         <FileNamePattern>${log.path}/sys-info.%d{yyyy-MM-dd}.log</FileNamePattern>
         <!-- 日志最大的历史 60天 -->
         <MaxHistory>60</MaxHistory>
      </rollingPolicy>
      <encoder>
         <pattern>${log.pattern}</pattern>
      </encoder>
      <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <!-- 过滤的级别 -->
            <level>INFO</level>
            <!-- 匹配时的操作：接收（记录） -->
            <onMatch>ACCEPT</onMatch>
            <!-- 不匹配时的操作：拒绝（不记录） -->
            <onMismatch>DENY</onMismatch>
        </filter>
   </appender>

   <appender name="file_error" class="ch.qos.logback.core.rolling.RollingFileAppender">
       <file>${log.path}/sys-error.log</file>
        <!-- 循环政策：基于时间创建日志文件 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 日志文件名格式 -->
            <fileNamePattern>${log.path}/sys-error.%d{yyyy-MM-dd}.log</fileNamePattern>
         <!-- 日志最大的历史 60天 -->
         <maxHistory>60</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>${log.pattern}</pattern>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <!-- 过滤的级别 -->
            <level>ERROR</level>
         <!-- 匹配时的操作：接收（记录） -->
            <onMatch>ACCEPT</onMatch>
         <!-- 不匹配时的操作：拒绝（不记录） -->
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

   <!-- Spring日志级别控制  -->
   <logger name="org.springframework" level="warn" />

   <root level="info">
      <appender-ref ref="console" />
   </root>

   <!--系统操作日志-->
    <root level="info">
        <appender-ref ref="file_info" />
        <appender-ref ref="file_error" />
    </root>
</configuration>
```

##  启动时个性化banner

在resource下新增banner.txt文件

[使用的banner图生成地址](http://www.network-science.de/ascii/)

```java
Application Name: ${app.name}
Application Version: ${app.version}
Spring Boot Version: ${spring-boot.version}

 _______   _______ .___  ___.   ______    __
|       \ |   ____||   \/   |  /  __  \  /_ |
|  .--.  ||  |__   |  \  /  | |  |  |  |  | |
|  |  |  ||   __|  |  |\/|  | |  |  |  |  | |
|  '--'  ||  |____ |  |  |  | |  `--'  |  | |
|_______/ |_______||__|  |__|  \______/   |_|
```

##  语言国际化

### 编写国际化文件

编写resource文件夹i18n内容

![构建6](https://yingjiusheng.github.io/images/springboot/6.png)

###  修改yaml文件资源配置

```yaml
spring:
    # 国际化资源信息
  messages:
    basename: i18n/message
```

###  添加config文件

```java
package com.demo1.framework.config;

import java.util.Locale;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.LocaleResolver;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
import org.springframework.web.servlet.i18n.LocaleChangeInterceptor;
import org.springframework.web.servlet.i18n.SessionLocaleResolver;

/**
 * 资源文件配置加载
 *
 */
@Configuration
public class I18nConfig implements WebMvcConfigurer
{
    @Bean
    public LocaleResolver localeResolver()
    {
        SessionLocaleResolver slr = new SessionLocaleResolver();
        // 默认语言
        slr.setDefaultLocale(Locale.SIMPLIFIED_CHINESE);
        return slr;
    }

    @Bean
    public LocaleChangeInterceptor localeChangeInterceptor()
    {
        LocaleChangeInterceptor lci = new LocaleChangeInterceptor();
        // 参数名
        lci.setParamName("lang");
        return lci;
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry)
    {
        registry.addInterceptor(localeChangeInterceptor());
    }
}
```

###  添加constant文件

```java
package com.demo1.common.constant;

public class I18nConstant {

    /**
     * 一个例子
     */
    public static final String TEXT = "not.null";

}
```

### 代码里使用

```java
package com.demo1.project.backend.controller;

import com.demo1.common.constant.I18nConstant;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.MessageSource;
import org.springframework.context.i18n.LocaleContextHolder;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import java.util.Locale;

@Controller
@RequestMapping("/backend")
public class IndexController {

    @Autowired
    private MessageSource messageSource;

    /**
     * 首页
     */
    @GetMapping("/index")
    @ResponseBody
    public String index() {
        Locale locale = LocaleContextHolder.getLocale();
        String msg = messageSource.getMessage(I18nConstant.TEXT, null, locale);
        Logger logger = LoggerFactory.getLogger(getClass());
        logger.info("这是国际化语言日志"+msg);
        return "index";
    }
}
```

### 访问

以浏览器语言为默认访问

链接访问 ?lang=zh_CN或?lang=en_US