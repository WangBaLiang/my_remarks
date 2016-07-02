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
