---
title: MySQL新版本设置root密码和重置root密码
date: 2018-08-03 10:45:54
description: MySQL新版本设置root密码和重置root密码
tags: MySQL
---

    > 摘要:最近在虚拟机上安装Ubuntu18.04版本,然后安装MySQL-server.安装MySQL-server过程中不再像之前会提示
    输入root账户的密码.所以需要进行对root账户设置密码,
    
    >操作环境:
        OS:Ubuntu 18.04
        MySQL版本:5.7及更高


### 1.前置条件

    需要系统的root账户或者使用sudo 命令

### 2.使用mysql_secure_installation进行对密码设置

    如果是第一次安装完MySQL后,可以使用:

    sudo mysql_secure_installation  对root账户进行设置密码操作

### 3.使用skip-grant-tables对root账户进行重置密码的操作

    3.1 停止当前正在运行的mysql服务

        sudo service mysql stop
    
    3.2 创建/var/run/mysqld目录,因为MySQL进程在启动和运行的时候都需要访问该soket文件

        sudo mkdir -p /var/run/mysqld
        sudo chown mysql:mysql /var/run/mysqld

    3.3 使用skip-grant-tables启动服务程序

        sudo /usr/sbin/mysqld --skip-grant-tables --skip-networking &

        jobs 然后确认下服务是否启动成功

    3.4 使用root无密码登录,进行修改设置密码操作

        mysql -u root root无密码登录

        FLUSH PRIVILEGES; 刷新一遍授权信息

        USE mysql; 切换到mysql库(安装好后自带的)

        UPDATE user SET authentication_string=PASSWORD("123456") WHERE User='root'; 设置密码字段的新密码,
        authentication_string是新版本存储密码的字段名,旧版本的是password.

        UPDATE user SET plugin="mysql_native_password" WHERE User='root';

        FLUSH PRIVILEGES;

        quit;

    3.5 重启MySQL服务

        sudo pkill mysqld  停掉之前启动的服务

        jobs 查看是否正确停止服务

        sudo service mysql start 启动MySQL服务

    3.6 使用root账户和刚设置的密码进行登录操作

        mysql -u root --password=123456  使用root和密码登录

        