---
title: MySQL字段值默认不区分大小写问题
date: 2018-05-13 10:42:33
description: mysql的字段值是默认不区分大小写的,但是用户在登录账户的时候严格区分大小写的
tags: MySQL
---

    摘要：
      mysql的字段值是默认不区分大小写的,但是用户在登录账户的时候严格区分大小写的,所以解决如下:

1 .在不改变表任何结构的情况下,可以直接在查询条件后面的字段名或者字段值作为binary()函数的参数即可,如下:

`select * from table_name t where binary(t.field) = 'Abc';`

2 .在建表的时候在字段后面加上binary,或者用alter语句来改变字段类型,只需要加上binary

    `mysql> create table t_user(

    -> username varchar(20) binary

    -> );`

对已有的表进行alert

`alter table table_name modify field varchar(20) binary`


注:table_name换成具体对应的表名称.field换成具体对应的表的字段
