---
layout: post
title:  "数据结构学习笔记"
date:   2021-09-07 17:02:31 +0800
categories: jekyll update
---


#### 链表

go 语言实现链表结构

```go
package main

import (
	"fmt"
	"sync"
)

// 节点存储的数据

type SingleObject interface{}

type SingleData struct {
    Key string
    Value string
}

// 单链表节点

type SingleNode struct {
    Data SingleData
    Next *SingleNode
}

// 单链表

type SingleList struct {
    mutex *sync.RWMutex
    Head *SingleNode
    Tail *SingleNode
    Size uint
}


// 初始化

func (list *SingleList) Init() {
    list.Size = 0
    list.Head = nil
    list.Tail = nil
    list.mutex = new(sync.RWMutex)
}

// 添加节点到链表尾部

func (list *SingleList) Append(node *SingleNode) bool {
    if node == nil {
        return false
    }
    list.mutex.Lock()
    defer list.mutex.Unlock()

    if list.Size == 0 {
        list.Head = node
        list.Tail = node
        list.Size = 1
        return true
    }

    tail := list.Tail
    tail.Next = node
    list.Size += 1
    return true
}

// 插入节点到指定位置

func (list *SingleList) Insert(index uint, node *SingleNode) bool {
    if node == nil {
        return false
    }

    if index > list.Size{
        return false
    }

    list.mutex.Lock()
    defer list.mutex.Unlock()

    if index == 0 {
        node.Next = list.Head
        list.Head = node
        list.Size += 1
        return true
    }

    var i uint
    ptr := list.Head
    for i = 1; i < index; i ++ {
        ptr = ptr.Next
    }
    next := ptr.Next
    ptr.Next = node
    node.Next = next
    list.Size += 1
    return true
}


// 删除节点

func (list *SingleList) Delete(index uint) bool {
    if list == nil || list.Size == 0 || index > list.Size - 1 {
        return false
    }

    list.mutex.Lock()
    defer list.mutex.Unlock()

    if index == 0 {
        head := list.Head.Next
        list.Head = head
        if list.Size == 1 {
            list.Tail = nil
        }
        list.Size -= 1
        return true
    }

    ptr := list.Head
    var i uint
    for i = 1; i < index; i ++ {
        ptr = ptr.Next
    }
    next := ptr.Next

    ptr.Next = next.Next
    if index == list.Size - 1 {
        list.Tail = ptr
    }
    list.Size -= 1
    return true
}

// 获取指定位置的节点，不存在则返回nil

func (list *SingleList) Get(index uint) *SingleNode {
    if list == nil || list.Size == 0 || index > list.Size - 1 {
        return nil
    }

    list.mutex.RLock()
    defer list.mutex.RUnlock()

    if index == 0 {
        return list.Head
    }
    node := list.Head
    var i uint
    for i = 0; i < index; i ++ {
        node = node.Next
    }
    return node
}

// 通过 key 获取 value

func (list *SingleList) GetValueByKey(key string) string {
    if list == nil || list.Size == 0 {
        return ""
    }
    list.mutex.RLock()
    defer list.mutex.RUnlock()

    head := list.Head
    if head.Data.Key == key {
        return head.Data.Value
    }
    for head.Next != nil {
        if head.Next.Data.Key == key {
            return head.Next.Data.Value
        }
        head = head.Next
    }
    return ""
}

// 打印链表

func (list *SingleList) Display() {
    if list == nil {
        fmt.Println("this single list is nil")
        return
    }

    list.mutex.RLock()
    defer list.mutex.RUnlock()

    fmt.Printf("this single list size is %d \n", list.Size)
    ptr := list.Head
    var i uint
    for i = 0; i < list.Size; i ++ {
        fmt.Printf("No%3d data is %v\n", i + 1, ptr.Data)
        ptr = ptr.Next
    }
}

func main() {
    list := new(SingleList)
    list.Init()
    node := &SingleNode{Data: &SingleData{Key: "test", Value: "test"}}
    list.Append(node)
    list.Display()
}
```