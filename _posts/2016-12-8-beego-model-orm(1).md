---
layout: post
title:  "beego-模型（models）-初认orm"
categories: go
tags:  go beego
---

* content
{:toc}

beego ORM 是一个强大的 Go 语言 ORM 框架。

<!--excerpt-->

## 安装


`go get github.com/astaxie/beego/orm`

## 特性

- 支持 Go 的所有类型存储
- 轻松上手，采用简单的 CRUD 风格
- 自动 Join 关联表
- 跨数据库兼容查询
- 允许直接使用 SQL 查询／映射
- 严格完整的测试保证 ORM 的稳定与健壮

## 简单实例

```go
package main

import (
	"fmt"

	"github.com/astaxie/beego/orm"
	_ "github.com/go-sql-driver/mysql"
)

type User struct {
	Id   int
	Name string `orm:"size(100)"`
}

func init() {
	// set default database
	orm.RegisterDataBase("default", "mysql", "root:@/test?charset=utf8", 30)

	// register model
	orm.RegisterModel(new(User))

	// create table
	orm.RunSyncdb("default", false, true)
}
func main() {
	//	fmt.Println("Hello World!")
	o := orm.NewOrm()

	user := User{Name: "slene"}

	// insert
	id, err := o.Insert(&user)
	fmt.Printf("ID: %d, ERR: %v\n", id, err)

	// update
	user.Name = "astaxie"
	num, err := o.Update(&user)
	fmt.Printf("NUM: %d, ERR: %v\n", num, err)

	// read one
	u := User{Id: user.Id}
	err = o.Read(&u)
	fmt.Printf("ERR: %v\n", err)

	//	delete
	num, err = o.Delete(&u)
	fmt.Printf("NUM: %d, ERR: %v\n", num, err)
}

```
要使用orm，最主要的就是init函数，下面看看init各个函数的意义

`	orm.RegisterDataBase("default", "mysql", "root:@/test?charset=utf8", 30)`

第一个参数是数据库的别名，用来切换数据库使用。
第二个是driverName，在RegisterDriver时注册的
第三是数据库连接字符串:root:123456@/test?charset=utf8相对于用户名:密码@数据库地址+名称?字符集（我的密码为空）
第四个参数相当于:
orm.SetMaxIdleConns("default", 30)

main函数里面的操作，就是对数据库的增删改查，是不是很简单！

## 关联查询

```go
	//关联查询
	var posts []*Post
	qs := o.QueryTable("post")
	num1, err := qs.Filter("User__Name", "11").All(&posts)
	fmt.Printf("NUM1: %d, ERR: %v\n", num1, err)

```

## SQL查询

```go

	//sql查询
	var maps []orm.Params
	num2, err := o.Raw("SELECT * FROM user").Values(&maps)
	fmt.Printf("NUM2: %d, ERR: %v\n", num2, err)
	for _, term := range maps {
		fmt.Println(term["id"], ":", term["name"])
	}
```






