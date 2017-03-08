---
layout: post
title:  "设计模式-观察者模式（go语言）"
categories: go
tags:  go 设计模式
---


* content
{:toc}


观察者模式（有时又被称为发布（publish ）-订阅（Subscribe）模式、模型-视图（View）模式、源-收听者(Listener)模式或从属者模式）是软件设计模式的一种。在此种模式中，一个目标物件管理所有相依于它的观察者物件，并且在它本身的状态改变时主动发出通知。这通常透过呼叫各观察者所提供的方法来实现。此种模式通常被用来实现事件处理系统。


<!--excerpt-->



## 实例分析

观察者模式包含如下角色：

- 目标（Subject）: 目标知道它的观察者。可以有任意多个观察者观察同一个目标。 提供注册和删除观察者对象的接口。

- 具体目标（ConcreteSubject）:  将有关状态存入各ConcreteObserver对象。

- 观察者(Observer):  为那些在目标发生改变时需获得通知的对象定义一个更新接口。当它的状态发生改变时, 向它的各个观察者发出通知。

- 具体观察者(ConcreteObserver):   维护一个指向ConcreteSubject对象的引用。存储有关状态，这些状态应与目标的状态保持一致。实现O b s e r v e r的更新接口以使自身状态与目标的状态保持一致。



## 实例代码



```go



// observer project main.go

package main



import (

	"container/list"

	"fmt"

)



type Subject interface {

	Attach(Observer)

	Detach(Observer)

	Notify()

}

type Observer interface {

	update(Subject)

}



type ConcreteSubject struct {

	observers *list.List

	value     int

}



func NewConcreteSubject() *ConcreteSubject {

	s := new(ConcreteSubject)

	s.observers = list.New()

	return s

}

func (s *ConcreteSubject) Attach(observer Observer) {

	s.observers.PushBack(observer)

}

func (s *ConcreteSubject) Detach(observer Observer) {

	for ob := s.observers.Front(); ob != nil; ob = ob.Next() {

		if ob.Value.(*Observer) == &observer {

			s.observers.Remove(ob)

			break

		}

	}

}

func (s *ConcreteSubject) Notify() {

	for ob := s.observers.Front(); ob != nil; ob = ob.Next() {

		ob.Value.(Observer).update(s)

	}

}

func (s *ConcreteSubject) setValue(v int) {

	s.value = v

	s.Notify()

}

func (s *ConcreteSubject) getValue() (value int) {

	return s.value

}



type ConcreteObserver1 struct {

}



func (o *ConcreteObserver1) update(subject Subject) {

	fmt.Println("observser1 value is ", subject.(*ConcreteSubject).getValue())

}



type ConcreteObserver2 struct {

}



func (o *ConcreteObserver2) update(subject Subject) {

	fmt.Println("observser2 value is ", subject.(*ConcreteSubject).getValue())

}



func main() {

	subject := NewConcreteSubject()

	observer1 := new(ConcreteObserver1)

	observer2 := new(ConcreteObserver2)

	subject.Attach(observer1)

	subject.Attach(observer2)

	subject.setValue(55)

	//	fmt.Println("Hello World!")

}



```



## 优缺点



### 优点



- 观察者模式可以实现表示层和数据逻辑层的分离,并定义了稳定的消息更新传递机制，抽象了更新接口，使得可以有各种各样不同的表示层作为具体观察者角色。



- 在观察目标和观察者之间建立一个抽象的耦合 ：一个目标所知道的仅仅是它有一系列观察者 , 每个都符合抽象的Observer类的简单接口。目标不知道任何一个观察者属于哪一个具体的类。这样目标和观察者之间的耦合是抽象的和最小的。因为目标和观察者不是紧密耦合的, 它们可以属于一个系统中的不同抽象层次。一个处于较低层次的目标对象可与一个处于较高层次的观察者通信并通知它 , 这样就保持了系统层次的完整。如果目标和观察者混在一块 , 那么得到的对象要么横贯两个层次 (违反了层次性), 要么必须放在这两层的某一层中(这可能会损害层次抽象)



- 支持广播通信 :不像通常的请求, 目标发送的通知不需指定它的接收者。通知被自动广播给所有已向该目标对象登记的有关对象。目标对象并不关心到底有多少对象对自己感兴趣 ;它唯一的责任就是通知它的各观察者。这给了你在任何时刻增加和删除观察者的自由。处理还是忽略一个通知取决于观察者。



- 观察者模式符合“开闭原则”的要求。



### 缺点



- 如果一个被观察者对象有很多的直接和间接的观察者的话，将所有的观察者都通知到会花费很多时间。



-  如果在观察者和观察目标之间有循环依赖的话，观察目标会触发它们之间进  行循环调用，可能导致系统崩溃。



- 观察者模式没有相应的机制让观察者知道所观察的目标对象是怎么发生变化的，而仅仅只是知道观察目标发生了变化。



