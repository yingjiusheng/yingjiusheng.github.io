---
layout: post
title:  "设计模式-抽象工厂模式（go语言）"
categories: go
tags:  go 设计模式
---

* content
{:toc}


抽象工厂模式就变得比工厂模式更为复杂，就像上面提到的缺点一样，工厂模式和简单工厂模式要求产品子类必须要是同一类型的，拥有共同的方法，这就限制了产品子类的扩展。于是为了更加方便的扩展，抽象工厂模式就将同一类的产品子类归为一类，让他们继承同一个抽象子类，我们可以把他们一起视作一组，然后好几组产品构成一族。

<!--excerpt-->

## 实例分析

   抽象工厂模式是工厂方法模式的升级版本，他用来创建一组相关或者相互依赖的对象。他与工厂方法模式的区别就在于，工厂方法模式针对的是一个产品等级结构；而抽象工厂模式则是针对的多个产品等级结构。在编程中，通常一个产品结构，表现为一个接口或者抽象类，也就是说，工厂方法模式提供的所有产品都是衍生自同一个接口或抽象类，而抽象工厂模式所提供的产品则是衍生自不同的接口或抽象类。

   在抽象工厂模式中，有一个产品族的概念：所谓的产品族，是指位于不同产品等级结构中功能相关联的产品组成的家族。抽象工厂模式所提供的一系列产品就组成一个产品族；而工厂方法提供的一系列产品称为一个等级结构。我们依然拿生产汽车的例子来说明他们之间的区别。

## 实例代码

```go

// abstruct_factory project main.go
package main

import (
	"fmt"
)

type IProduct interface {
	show()
}
type Product1 struct {
}
type Product2 struct {
}

func (product *Product1) show() {
	fmt.Println("this is product 1 !!!")
}
func (product *Product2) show() {
	fmt.Println("this is product 2 !!!")
}

type IFactory interface {
	createProduct1()
	createProduct2()
}
type Factory struct {
}

func (factory *Factory) createProduct1() (product1 *Product1) {
	product1 = new(Product1)
	return
}
func (factory *Factory) createProduct2() (product2 *Product2) {
	product2 = new(Product2)
	return
}
func main() {
	factory := new(Factory)
	factory.createProduct1().show()
	factory.createProduct2().show()
	//fmt.Println("Hello World!")
}

```

## 优缺点

### 优点

  抽象工厂模式除了具有工厂方法模式的优点外，最主要的优点就是可以在类的内部对产品族进行约束。所谓的产品族，一般或多或少的都存在一定的关联，抽象工厂模式就可以在类内部对产品族的关联关系进行定义和描述，而不必专门引入一个新的类来进行管理。

###  缺点

 产品族的扩展将是一件十分费力的事情，假如产品族中需要增加一个新的产品，则几乎所有的工厂类都需要进行修改。所以使用抽象工厂模式时，对产品等级结构的划分是非常重要的。