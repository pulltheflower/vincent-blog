---
layout: post
title:  "Redis 语言学习笔记"
date:   2021-09-07 17:02:31 +0800
categories: jekyll update
---

### 简介

Redis 是基于 c 语言编写的开源非关系型内存数据库，可以用作数据库、缓存、消息中间件。


### 数据类型 

1. `String` : 字符串类型
2. `List` : 列表类型
3. `Set` : 无序集合类型
4. `ZSet` : 有序集合类型
5. `Hash` : 哈希表类型


### Redis 核心对象

`redisObject`， 用这个结构体来表示 String、Hash、List、Set、ZSet 五种数据类型。

`redisObject` 字段
``` c
struct redisObject{
  type
  encoding
  ptr
  vm
  others
}
```


| 数据类型 |编码方式  | 
| --- | --- | 
| String |embstr  | 
|  | raw | 
|  | int | 
| Hash |ht  | 
|  | ziplist | 
| List | linkedlist | 
|  | ziplist | 
| Set | ht | 
|  | inset | 
| ZSet | ziplist | 
|  | skiplist | 

##### 不同类型和编码的对象

| 类型 | 编码 | 对象 |
| --- | --- | --- |
| REDIS_STRING | REDIS_ENCODING_INT | 使用整数值实现的字符串对象 |
| REDIS_STRING | REDIS_ENCODING_EMBSTR | 使用 embstr 编码的简单动态字符串实现的字符串对象 |
| REDIS_STRING | REDIS_ENCODING_RAW | 使用简单动态字符串实现的字符串对象 |
| REDIS_LIST | REDIS_ENCODING_ZIPLIST | 使用压缩列表实现的列表对象 |
| REDIS_LIST | REDIS_ENCODING_LINKEDLIST | 使用双端链表实现的列表对象 |
| REDIS_HASH | REDIS_ENCODING_HT | 使用字典实现的哈希对象 |
| REDIS_HASH | REDIS_ENCODING_ZIPLIST | 使用压缩列表实现的哈希对象 |
| REDIS_SET | REDIS_ENCODING_INTSET | 使用整数集合实现的集合对象 |
| REDIS_SET | REDIS_ENCODING_HT | 使用字典实现的集合对象 |
| REDIS_ZSET | REDIS_ENCODING_ZIPLIST | 使用压缩列表实现的有序集合对象 |
| REDIS_ZSET | REDIS_ENCODING_SKIPLIST | 使用跳跃表和字典实现的有序集合对象 |
