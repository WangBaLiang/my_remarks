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

##数据库操作问题
* 描述：

            代码：
            delete_user_sql = '''
            DELETE FROM user WHERE id=%d;
            DELETE FROM user_week WHERE u_id=%d;
            UPDATE hospital SET user_number=user_number-1 WHERE id=%d;
            ''' % (user_id,user_id,hospital_id)

            try:
                cursor.execute(delete_user_sql)
                print delete_user_sql
            except MySQLdb.Error, e:
                print e
            finally:
                cursor.close()
                db.close()

            数据库变量，事务自动提交打开：
            mysql> show variables like "%autocommit%";
            +---------------+-------+
            | Variable_name | Value |
            +---------------+-------+
            | autocommit    | ON    |
            +---------------+-------+
            1 row in set (0.00 sec)


上述代码在测试时没有问题，上线时，切换了Mysql引擎为InnoDB，结果执行该段代码不报错，但未成功。
* 原因：[stackovberflow answer](http://stackoverflow.com/questions/1451782/python-mysql-selects-work-but-not-deletes)
* InnoDB支持事务，[MySQLdb在1.2后默认不支持autocommit](http://mysql-python.sourceforge.net/FAQ.html#my-data-disappeared-or-won-t-go-away)，需要在DML语句后执行db.commit()


