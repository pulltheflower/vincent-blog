---
layout: post
title:  "Sql 语言学习笔记"
date:   2021-09-07 17:02:31 +0800
categories: jekyll update
---


#### 差集查询

###### 查找在 `dept` 表中存在而在 `emp` 表中不存在的 deptno
```sql
# PostgreSQL
select deptno from dept
except
select deptno from emp

# Mysql
select deptno
  from dept
 where deptno not in (select deptno from emp)
```


