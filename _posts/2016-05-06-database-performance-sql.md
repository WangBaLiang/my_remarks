---
layout: post
title: 数据库性能优化之SQL语句
date: 2016-05-06 10:05
---
#[数据库性能优化之SQL语句](http://blog.jobbole.com/96996/)
##操作符优化
1. IN 操作符
    * 先尝试转换成多个表连接，若转换不成功，执行in中的子查询，
