---
layout: post
title:  "设计模式-单例模式（go语言）"
categories: go
tags:  go 设计模式
---

* content
{:toc}

“保证一个类仅有一个实例，并提供一个访问他的全局访问点”---这是对单例模式最简单的定义；

<!--excerpt-->

## 代码实现

 1. 不考虑并发的单例模式

```go

// Singleton project main.go
package main

import (
	"fmt"
)

var m *Manager

func GetInstance(name string) *Manager {
	if m == nil {
		m = &Manager{name: name}
	}
	return m
}

type Manager struct {
	name string
}

func (this *Manager) say_name() {
	fmt.Println(this.name)
}

func main() {
	man1, man2 := GetInstance("hello"), GetInstance("world")
	man1.say_name()
	man2.say_name()
}


```
   2.并发情况下的单例模式


  并发情况下，上面的代码并不能保证只创建一个实例（当在并发的情况下，当第一个goroutine执行到m = &Manager{name: name}之前，第二个goroutine也正好获取实例，此时判断m是否为空，因为m = &Manager{name: name}还未执行，m为非空，这时候代码就要执行了两次），为了解决这个问题，我们引入go的锁机制。

```go
// Singleton project main.go
package main

import (
	"fmt"
	"sync"
)

var m *Manager
var lock *sync.Mutex = &sync.Mutex{}

func GetInstance(name string) *Manager {
	if m == nil {
		lock.Lock()
		defer lock.Unlock()
		if m == nil {
			m = &Manager{name: name}
		}

	}
	return m
}

type Manager struct {
	name string
}

func (this *Manager) say_name() {
	fmt.Println(this.name)
}

func main() {
	man1, man2 := GetInstance("hello"), GetInstance("world")
	man1.say_name()
	man2.say_name()
}


```

  3.go专属的单例模式

因为单例模式之创建一个实例，go中sync.Once中有个Do方法，可以保证代码只被执行一次！！！

```go
package main

import (
	"fmt"
	"sync"
)

var m *Manager

var once sync.Once

func GetInstance(name string) *Manager {
	once.Do(func() {
		m = &Manager{name: name}
	})
	return m
}

type Manager struct {
	name string
}

func (this *Manager) say_name() {
	fmt.Println(this.name)
}

func main() {
	man1, man2 := GetInstance("hello"), GetInstance("world")
	man1.say_name()
	man2.say_name()
}
```


