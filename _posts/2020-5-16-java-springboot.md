---
layout: post
title:  "SpringBoot的学习（三）"
categories: java SpringBoot
tags: java
---

* content
{:toc}
记录springBoot框架的学习。

<!--excerpt-->

##  配置Druid数据源

### 概念

Druid为监控而生的数据库连接池，它是阿里巴巴开源平台上的一个项目。Druid是Java语言中最好的数据库连接池，Druid能够提供强大的监控和扩展功能.它可以替换DBCP和C3P0连接池。**Druid提供了一个高效、功能强大、可扩展性好的数据库连接池。**

###  引入依赖

```xml
<druid.version>1.1.14</druid.version>
        <!--阿里数据库连接池 -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>${druid.version}</version>
        </dependency>
        <!--   基本jdbc     -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <!-- Mysql驱动包 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
```

###  修改yml配置文件

```yaml
spring:
    datasource:
      type: com.alibaba.druid.pool.DruidDataSource
      driverClassName: com.mysql.cj.jdbc.Driver
      username: vagrant
      password: 123456
      url: jdbc:mysql://localhost:3306/java_demo1
```

此时启动即可

### 配置Druid的其他参数

DruidConfig

@Configuration
public class DruidConfig {

```java
@ConfigurationProperties(prefix = "spring.datasource")
@Bean
public DataSource druid(){
   return  new DruidDataSource();
}

//配置Druid的监控
//1、配置一个管理后台的Servlet
@Bean
public ServletRegistrationBean statViewServlet(){
    ServletRegistrationBean bean = new ServletRegistrationBean(new StatViewServlet(), "/druid/*");
    Map<String,String> initParams = new HashMap<>();

    initParams.put("loginUsername","admin");
    initParams.put("loginPassword","123456");
    initParams.put("allow","");//默认就是允许所有访问
    initParams.put("deny","192.168.15.21");

    bean.setInitParameters(initParams);
    return bean;
}
```


```java
//2、配置一个web监控的filter
@Bean
public FilterRegistrationBean webStatFilter(){
    FilterRegistrationBean bean = new FilterRegistrationBean();
    bean.setFilter(new WebStatFilter());

    Map<String,String> initParams = new HashMap<>();
    initParams.put("exclusions","*.js,*.css,/druid/*");

    bean.setInitParameters(initParams);

    bean.setUrlPatterns(Arrays.asList("/*"));

    return  bean;
}
```
### 添加其他Druid参数

```yaml
initialSize: 5
minIdle: 5
maxActive: 20
maxWait: 60000
timeBetweenEvictionRunsMillis: 60000
minEvictableIdleTimeMillis: 300000
validationQuery: SELECT1FROMDUAL
testWhileIdle: true
testOnBorrow: false
testOnReturn: false
logSlowSql: true
```

### 配置成功

![图7][https://yingjiusheng.github.io/images/springboot/7.png]

### Druid的多数据源配置

`待学习`