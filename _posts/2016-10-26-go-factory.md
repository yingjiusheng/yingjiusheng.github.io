---
layout: post
title:  "设计模式-工厂方法模式（go语言）"
categories: go
tags:  go 设计模式
---

* content
{:toc}


工厂模式基本与简单工厂模式差不多，上面也说了，每次添加一个产品子类都必须在工厂类中添加一个判断分支，这样违背了开放-封闭原则，因此，工厂模式就是为了解决这个问题而产生的。

<!--excerpt-->

## 实例分析

既然每次都要判断，那我就把这些判断都生成一个工厂子类，这样，每次添加产品子类的时候，只需再添加一个工厂子类就可以了。这样就完美的遵循了开放-封闭原则。但这其实也有问题，如果产品数量足够多，要维护的量就会增加，好在一般工厂子类只用来生成产品类，只要产品子类的名称不发生变化，那么基本工厂子类就不需要修改，每次只需要修改产品子类就可以了。

同样工厂模式一般应该于程序中大部分地方都只使用其中一种产品，工厂类也不用频繁创建产品类的情况。这样修改的时候只需要修改有限的几个地方即可。

## 实例代码

```go
// factory project main.go
package main

import (
	"fmt"
)

type BaseOperation struct {
	num1 float64
	num2 float64
}
type OperationAdd struct {
	BaseOperation
}
type OperationSub struct {
	BaseOperation
}
type Operation interface {
	getResult() float64
	setNumA(float64)
	setNumB(float64)
}

//-----------------------
type Factory interface {
	createOperation() Operation
}

type AddFactory struct {
}

func (a *AddFactory) createOperation() (operation Operation) {
	operation = new(OperationAdd)
	return
}

//-----------------------
type SubFactory struct {
}

func (s *SubFactory) createOperation() (operation Operation) {
	operation = new(OperationSub)
	return
}

//-----------------------
func (operation *BaseOperation) setNumA(numA float64) {
	operation.num1 = numA
}
func (operation *BaseOperation) setNumB(numB float64) {
	operation.num2 = numB
}
func (operation *OperationAdd) getResult() float64 {
	return operation.num1 + operation.num2
}
func (operation *OperationSub) getResult() float64 {
	return operation.num1 - operation.num2
}

//-----------------------
func main() {
	add_factory := new(AddFactory)
	add_oper := add_factory.createOperation()
	add_oper.setNumA(10)
	add_oper.setNumB(20)
	fmt.Println(add_oper.getResult())
	//	fmt.Println("Hello World!")
}

```

## 优缺点

### 优点

基本与简单工厂模式一致，多的一点优点就是遵循了开放-封闭原则，使得模式的灵活性更强。

### 缺点

与简单工厂模式差不多。
