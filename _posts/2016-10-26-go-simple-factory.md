---
layout: post
title:  "设计模式-简单工厂模式（go语言）"
categories: go
tags:  go 设计模式
---

* content
{:toc}


简单工厂模式是工厂模式中最简单的一种，他可以用比较简单的方式隐藏创建对象的细节，一般只需要告诉工厂类所需要的类型，工厂类就会返回需要的产品类，但客户端看到的只是产品的抽象对象，无需关心到底是返回了哪个子类。

<!--excerpt-->


## 实例分析

   这个模式本身很简单而且使用在业务较简单的情况下。一般用于小项目或者具体产品很少扩展的情况（这样工厂类才不用经常更改）。
 它由三种角色组成：
   1. 工厂类角色：这是本模式的核心，含有一定的商业逻辑和判断逻辑，根据逻辑不同，产生具体的工厂产品。如例子中的OperationFactory类。
   2. 抽象产品角色：它一般是具体产品继承的父类或者实现的接口。由接口或者抽象类来实现。如例中的BaseOperation接口。
   3. 具体产品角色：工厂类所创建的对象就是此角色的实例。在java中由一个具体类实现，如OperationAdd、OperationSub类。

## 实例代码

```php
package main

import (
	"fmt"
)

type Operation interface {
	getResult() float64
	setNumA(float64)
	setNumB(float64)
}

type BaseOperation struct {
	numberA float64
	numberB float64
}

func (Operation *BaseOperation) setNumA(numA float64) {
	Operation.numberA = numA
}
func (Operation *BaseOperation) setNumB(numB float64) {
	Operation.numberB = numB
}

type OperationAdd struct {
	BaseOperation
}

func (this *OperationAdd) getResult() float64 {
	return this.numberA + this.numberB
}

type OperationSub struct {
	BaseOperation
}

func (this *OperationSub) getResult() float64 {
	return this.numberA - this.numberB
}

type OperationFactory struct {
}

func (this OperationFactory) createOperation(operator string) (operation Operation) {
	switch operator {
	case "+":
		operation = new(OperationAdd)
	case "-":
		operation = new(OperationSub)
	default:
		panic("error")
	}
	return
}

func main() {
	defer func() {
		if err := recover(); err != nil {
			fmt.Println(err)
		}
	}()
	var fac OperationFactory
	oper := fac.createOperation("+")
	oper.setNumA(4.0)
	oper.setNumB(5.0)
	fmt.Println(oper.getResult())
}

```

## 优缺点

### 优点

1.隐藏了对象创建的细节，将产品的实例化推迟到子类中实现。

2.客户端基本不用关心使用的是哪个产品，只需要知道用哪个工厂就行了，提供的类型也可以用比较便于识别的字符串。

3.方便添加新的产品子类，每次只需要修改工厂类传递的类型值就行了。

4.遵循了依赖倒转原则。

### 缺点

1.要求产品子类的类型差不多，使用的方法名都相同，如果类比较多，而所有的类又必须要添加一种方法，则会是非常麻烦的事情。或者是一种类另一种类有几种方法不相同，客户端无法知道是哪一个产品子类，也就无法调用这几个不相同的方法。

2.每添加一个产品子类，都必须在工厂类中添加一个判断分支，这违背了开放-封闭原则。