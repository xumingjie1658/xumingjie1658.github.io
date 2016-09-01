---
layout: post
title: "学习mongdb"
description: ""
category: [mongdb, chinese]
tags: [mongdb]
---
{% include setup %}

mongod是mongodb的服务，mongo是mongodb的管理后台

---

## mongdb中的基本概念

SQL中概念与MongDB中的对应关系如下：

```
database -> database
table -> collection
row -> document
column -> field
index -> index
table joins -> none
primary key -> primary key
```

![mysql数据]({{ BLOG_IMG }}mongdb1.png)

![mongdb数据]({{ BLOG_IMG }}mongdb2.png)

### 数据库

```
show dbs *查看所有数据库*
db *查看当前数据库*
use local *新建或切换数据库*
db.dropDatabase() *删除当前数据库*
db.col.insert({x:1}) *向集合col中插入一个文档*
db.col.update({'title':'MongoDB 教程'},{$set:{'title':'MongoDB'}},{multi:true}) *更新已存在的文档,multi代表更新多条*
db.collection.save *使用传入的文档替换现有的文档*
db.collection.remove *删除集合中的文档*
```

### 文档

文档是一个键值(key-value)对(即BSON)

.和$有特别的意义，只有在特定环境下才能使用

相同的字段不需要相同的数据类型

MongoDB区分类型和大小写

### 集合

集合存在于数据库中，集合没有固定的结构，这意味着你在对集合可以插入不同格式和类型的数据，但通常情况下我们插入集合的数据都会有一定的关联性。

### 条件表达式

(>) 大于 - $gt

(<) 小于 - $lt

(>=) 大于等于 - $gte

(<= ) 小于等于 - $lte

