---
layout: post
title: MySQL安装及使用
categories: [数据库]
tags: [MySQL]
disqus: y
---

##MySQL源码安装记录
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
        shell> scripts/mysql_install_db --user=mysql --base_dir=/usr/local/mysql --datadir=/usr/local/mysql/data
        shell> chown -R root .
        shell> chown -R mysql data
        # Next command is optional
        shell> cp support-files/my-medium.cnf /etc/my.cnf
        shell> bin/mysqld_safe --user=mysql &
        # Next command is optional
        shell> cp support-files/mysql.server /etc/init.d/mysql.server


3. 安装完成后，无法启动，报错“Fatal error: Can't open and lock privilege tables: Table 'mysql.user' doesn't exist” 
4. 查看/usr/local/mysql/data/mysql目录没有数据，回想了下，在第2步中，解压之后是没有建data目录的
5. 删除data目录下的数据（还未使用没有重要数据），检查目录的权限，具体看第2步
6. 执行bin/mysqld --initialize --user=mysql，会默认给root指定一个密码，然后启动数据库
7. 修改root密码，直接使用下面语句会报错：ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.

        UPDATE mysql.user SET Password=PASSWORD('your_new_password') WHERE User='root'; 

8. 参考[stackoverflow](http://stackoverflow.com/questions/33467337/reset-mysql-root-password-using-alter-user-statement-after-install-on-mac)上的回答
9. 在[官方文档](https://dev.mysql.com/doc/refman/5.6/en/alter-user.html)中有介绍：

        Password expiration for an account affects the corresponding row of the mysql.user table: The server sets the password_expired column to 'Y'.

        A client session operates in restricted mode if the account password has been expired. In restricted mode, operations performed within the session result in an error until the user establishes a new account password:

10. 密码有过期时间，一旦过期进入严格模式就会导致7的错误
11. SET PASSWORD = PASSWORD('new_password');然后就可以重新设置密码

##MySQL Ubuntu安装

        tar -xvf mysql-server_MVER-DVER_CPU.deb-bundle.tar
        sudo apt-get install libaio1
        sudo dpkg-preconfigure mysql-community-server_*.deb
        sudo dpkg -i mysql-{common,community-client,client,community-server,server}_*.deb
        // 如果提示依赖错误，执行
        sudo apt-get -f install


##MySQL Ubuntu卸载
        
        # 查看安装
        dpkg -l |grep mysql
        dpkg -P 彻底卸载
        dpkg -r


##命令备忘


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


##问题备忘
###已为远程访问赋权，报错2003 can't connect to mysql server on
注释my.cnf中的    bind-address = 127.0.0.1


###can't read from messagefile '/usr/share/mysql/english/errmsg.sys'
cp /usr/local/mysql/share/english/errmsg.sys /usr/share/mysql/english/errmsg.sys







