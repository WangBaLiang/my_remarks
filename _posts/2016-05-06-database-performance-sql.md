---
layout: post
title: 数据库性能优化之SQL语句
published: false
categories: 数据库
tags: 数据库 性能
date: 2016-05-05 10:00
---
#[数据库性能优化之SQL语句](http://blog.jobbole.com/96996/)
##操作符优化
1. IN 操作符
    * 先尝试转换成多个表连接，若转换不成功，执行in中的子查询，