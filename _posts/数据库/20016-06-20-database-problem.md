---
layout: post
title: 数据库问题记录
categories: [数据库]
tags: [数据库, 问题]
disqus: y
---
#版本问题
1292，"Incoorrect datetime value:'2016-06-30 14:21:13.487547+08:00' for column 'send_time'"      
5.5版本MySQL没有问题，5.7版本报错     
send_time列是datetime类型       
经测试，5.7可以插入"2016-06-29 18:16:24.731254"数据      


##datetime和timestamp类型
    
