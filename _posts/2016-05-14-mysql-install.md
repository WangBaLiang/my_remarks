---
layout: post
titile: MySQL安装记录
disqus: y
---

##MySQL安装记录
1. 版本5.7，参照[官方文档](https://dev.mysql.com/doc/refman/5.7/en/binary-installation.html)
2. 下面是官方提供的命令

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
