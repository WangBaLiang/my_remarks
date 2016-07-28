---
layout: post
title: MySQL安装及使用
categories: [数据库]
tags: [MySQL]
disqus: y
---
## 下载地址

5.7二进制包
wget -c http://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.13-linux-glibc2.5-x86_64.tar.gz

## MySQL二进制包安装记录

1. 版本5.7，参照[官方文档](https://dev.mysql.com/doc/refman/5.7/en/binary-installation.html)
2. 下面是官方提供的命令

        apt-get install libaio1
        5.7
        shell> groupadd mysql
        shell> useradd -r -g mysql -s /bin/false mysql
        shell> cd /usr/local
        shell> tar zxvf /path/to/mysql-VERSION-OS.tar.gz
        shell> ln -s full-path-to-mysql-VERSION-OS mysql
        shell> cd mysql
        shell> mkdir mysql-files
        shell> chmod 750 mysql-files
        shell> chown -R mysql .
        shell> chgrp -R mysql .
        shell> bin/mysql_install_db --user=mysql    # Before MySQL 5.7.6
        shell> bin/mysqld --initialize --user=mysql # MySQL 5.7.6 and up
        shell> bin/mysql_ssl_rsa_setup              # MySQL 5.7.6 and up
        shell> chown -R root .
        shell> chown -R mysql data mysql-files
        shell> bin/mysqld_safe --user=mysql &
        # Next command is optional
        shell> cp support-files/mysql.server /etc/init.d/mysql.server

        5.5
        shell> groupadd mysql
        shell> useradd -r -g mysql -s /bin/false mysql
        shell> cd /usr/local
        shell> tar zxvf /path/to/mysql-VERSION-OS.tar.gz
        shell> ln -s full-path-to-mysql-VERSION-OS mysql
        shell> cd mysql
        shell> chown -R mysql .
        shell> chgrp -R mysql .
        shell> scripts/mysql_install_db --user=mysql
        shell> chown -R root .
        shell> chown -R mysql data
        # Next command is optional
        shell> cp support-files/my-medium.cnf /etc/my.cnf
        shell> bin/mysqld_safe --user=mysql &
        # Next command is optional
        shell> cp support-files/mysql.server /etc/init.d/mysql.server

        UPDATE mysql.user SET Password=PASSWORD('your_new_password') WHERE User='root';

8. 参考[stackoverflow](http://stackoverflow.com/questions/33467337/reset-mysql-root-password-using-alter-user-statement-after-install-on-mac)上的回答
9. 在[官方文档](https://dev.mysql.com/doc/refman/5.6/en/alter-user.html)中有介绍：

        Password expiration for an account affects the corresponding row of the mysql.user table: The server sets the password_expired column to 'Y'.

        A client session operates in restricted mode if the account password has been expired. In restricted mode, operations performed within the session result in an error until the user establishes a new account password:

10. 密码有过期时间，一旦过期进入严格模式就会导致7的错误
11. SET PASSWORD = PASSWORD('new_password');然后就可以重新设置密码

## mysqldiff安装与使用

* [下载地址](http://cdn.mysql.com//Downloads/MySQLGUITools/mysql-utilities-1.5.6.tar.gz)
* python安装
* 使用 mysqldiff --server1=root:passwd@ip:3306 --difftype=sql --force dev1:dev2

## MySQL Ubuntu安装与卸载

        tar -xvf mysql-server_MVER-DVER_CPU.deb-bundle.tar
        sudo apt-get install libaio1
        sudo dpkg-preconfigure mysql-community-server_*.deb
        sudo dpkg -i mysql-{common,community-client,client,community-server,server}_*.deb
        // 如果提示依赖错误，执行
        sudo apt-get -f install
        # 查看安装
        dpkg -l |grep mysql
        dpkg -P 彻底卸载
        dpkg -r


## 命令备忘


        # 导出数据和表结构
        mysqldump -u用户名 -p密码 数据库名 > 数据库名.sql

        # 只导出表结构
        mysqldump -u用户名 -p密码 -d 数据库名 > 数据库名.sql

        # 创建数据库
        mysql>create database abc character set utf-8;

        # 导入sql
        # 方法一
        mysql>use abc
        mysql>set names utf8;
        mysql>source /path/to/abc.sql;
        # 方法二
        mysql -u用户名 -p密码 数据库名 < 数据库名.sql

        # 远程访问赋权
        #方法一：授权
        use mysql
        # 语法：grant 权限 on 数据库对象 to 用户
        grant all privileges on *.* to root@'%' identified by "root";
        # 方法二：改表
        update user set host = '%' where user ='root';
        flush privileges;

        #修改root密码
        #第一种
        mysql> SET PASSWORD FOR 'root'@'localhost' = PASSWORD('newpass');
        #第二种
        mysqladmin -u root password "newpass"
        mysqladmin -u root password oldpass "newpass"
        #第三种
        mysql> use mysql;
        mysql> UPDATE user SET Password = PASSWORD('newpass') WHERE user = 'root';
        mysql> FLUSH PRIVILEGES;
        #忘记root密码
        mysqld_safe --skip-grant-tables&
        mysql -u root mysql
        mysql> UPDATE user SET password=PASSWORD("new password") WHERE user='root';
        mysql> FLUSH PRIVILEGES;



## 问题备忘

### 已为远程访问赋权，报错2003 can't connect to mysql server on

注释my.cnf中的    bind-address = 127.0.0.1

### can't read from messagefile '/usr/share/mysql/english/errmsg.sys'

cp /usr/local/mysql/share/english/errmsg.sys /usr/share/mysql/english/errmsg.sys


### Fatal error: Can't open and lock privilege tables: Table 'mysql.user' doesn't exist

我遇到的原因如下：
安装时没有指定datadir，默认应该装在/var/lib/mysql下，但是my.cnf里的datadir不是这个路径。
还有可能，安装时指定了路径，my.cnf配的路径也一样，但是却还是报这个错。原因是其他路径下可能有残留的my.cnf，导致配置覆盖。

        $ find / -name my.cnf
        /etc/my.cnf
        /etc/mysql/my.cnf


### Can't start server : Bind on unix socket: Permission denied

5.7的折腾不过，装5.5的，报这个错
解决方法：把my.cnf里的socket位置定为权限/tmp下就好

### unable to lock ./ibdata1 error 11

搜到一个说法是磁盘空间不够，检查了，不是。
啥原因，不知道，折腾了下，我靠，好了。
