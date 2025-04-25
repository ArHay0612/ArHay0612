---
title: MySQL中utf8和utf8mb4区别对emoji支持
date: 2019-04-27 11:13:08
description: utf8mb4是utf8的超集，除了将编码改为utf8mb4外不需要做其他转换。
tags: MySQL
---

    摘要:
      MySQL在5.5.3之后增加了这个utf8mb4的编码，mb4就是most bytes 4的意思，专门用来兼容
      四字节的unicode。好在utf8mb4是utf8的超集，除了将编码改为utf8mb4外不需要做其他转换。
      当然，为了节省空间，一般情况下使用utf8也就够了.
      查看当前的MySQL版本:
      mysql> select version();
      或者
      mysql --version

### utf8不支持emoji表情的问题

  当使用utf8字符集的时候,插入emoji表情符号会提示" Incorrect string value: '\xXX\xXX\xXX\xXX' for column......",原因在于MySQL中utf8字符集只支持三字节UTF-8编码的Unicode范围，而emoji字符属于四字节编码部分.此时,需要将库表的字符集更改为utf8mb4

### 修改字符集为utf8mb4

  修改/etc/mysql/my.cnf文件或者/etc/mysql/mysql.conf.d/mysqld.cnf文件,修改以下参数:

```
[client]
default-character-set=utf8mb4
  
  
[mysql]
default-character-set=utf8mb4
  
  
[mysqld]
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci
init_connect = 'SET NAMES utf8mb4'
character-set-client-handshake = false
```

    注:
    1.init_connect='SET NAMES utf8mb4' 表示初始化连接都设置为utf8mb4字符集;
    2.skip-character-set-client-handshake = true 忽略客户端字符集设置，不论客户端是何种字符集，都按照init_connect中的设置进行使用

### 对数据库相关的表进行字符集修改

#### 建立新库和表的情况,直接使用utf8mb4字符

```
CREATE DATABASE IF NOT EXISTS test default charset utf8mb4 COLLATE utf8mb4_unicode_ci;

CREATE TABLE `t_table`  (
  `id` varchar(36) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL,
  `create_time` datetime(0) NULL DEFAULT NULL,
  'comment' varchar(100) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8mb4 COLLATE = utf8mb4_unicode_ci ROW_FORMAT = Dynamic;
```

#### 已经存在表的情况,对库,表和字段都修改为utf8mb4

```
mysql> ALTER DATABASE test CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

mysql>ALTER TABLE `t_table` CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

mysql>ALTER TABLE `t_table` MODIFY COLUMN `comment`  varchar(100) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### 重启MySQL服务

```
/etc/init.d/mysqld restart 或者service mysql restart
```

### 登录数据库查看字符集是否更改成功

```
mysql> SHOW VARIABLES WHERE Variable_name LIKE 'character%' OR Variable_name LIKE 'collation%';

+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8mb4                    |
| character_set_connection | utf8mb4                    |
| character_set_database   | utf8mb4                    |
| character_set_filesystem | binary                     |
| character_set_results    | utf8mb4                    |
| character_set_server     | utf8mb4                    |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
| collation_connection     | utf8mb4_unicode_ci         |
| collation_database       | utf8mb4_unicode_ci         |
| collation_server         | utf8mb4_unicode_ci         |
+--------------------------+----------------------------+

```

    关于Windows下MySQL的一点坑:
    之前一个旧式的服务器采用的是Windows server2012,mysql使用的是安装版的.安装路径在
    C:\Program Files\MySQL\MySQL Server 5.6下.有个my-default.ini配置文件,
    但是无论对这个文件如何配置修改,重启服务器都无效.经过多方搜索,发现Windows下MySQL服务
    默认使用的不是该文件,而是采用C:\ProgramData\MySQL\MySQL Server 5.6下的my.ini
    这个文件.所以需要对这个文件修改才能使其生效.