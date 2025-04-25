---
title: MySQL设置用户远程登录
date: 2018-06-12 10:59:34
description: MySQL设置用户远程登录
tags: MySQL
---

    摘要:在Linux(debian)下,通过apt install mysql-server后,输入root账户密码后,
    默认是不允许远程登录的.可以用过以下几种方式来设置允许能够远程登录

1.改mysql库下的user表的数据

    mysql -u root –p
    mysql>use mysql;
    mysql>update user set host = '%' where user = 'root';
    mysql>select host, user from user;

2.通过授权的方式,这种方式可以对不同的用户设置不同的访问权限

    #例如: 在MySQL服务器主机上执行,允许root使用123456从任何主机连接到mysql服务器

    mysql>GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;

    mysql>FLUSH PRIVILEGES; # 刷新权限,使配置生效

    #例如:允许用户test从ip为120.77.163.89的主机连接到mysql服务器，并使用123456作为密码

    mysql>GRANT ALL PRIVILEGES ON *.* TO 'test'@’120.77.163.89’ IDENTIFIED BY '654321' WITH GRANT OPTION;

    mysql>FLUSH PRIVILEGES; # 刷新权限,使配置生效
