---
layout: post
title: Python错误记录
categories: [编程]
tags: [socket]
disqus: y
---
##编码问题
1. 'ascii' codec can't decode byte 0xe6 in position 2: ordinal not in range(128)
    1. 这个问题，最直接的解决方法是把字符encode('utf-8')，注意，是所有的字符

##安装依赖问题
1. 安装Scrapy：libxml/xmlversion.h: No such file or directory
    yum install libxslt-devel -y

##性能问题
###sql join对性能的影响
使用sqlalchemy做数据库相关操作，后台的某个逻辑是，取出多个表的数据，然后处理数据转换成[{}]的形式。     
但“多个表”的范围不一定，可能是三个表，可能是四个表，涉及到多个join，对两种写法进行了测试。    
join三个表的处理逻辑，费时4.89091873169ms     
join四个表的处理逻辑，费时52.2291660309ms   
慢了十倍