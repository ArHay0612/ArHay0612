---
title: 'tomcat解决java.lang.IllegalArgumentException: Invalid character异常'
date: 2019-02-21 11:06:41
description: IllegalArgumentException
tags: tomcat
---

>tomcat新版添加了对于http头的验证。出现java.lang.IllegalArgumentException: Invalid character found in the request target. The valid char... 异常

网上查找了几种方法归类

1.更换tomcat版本,但是7,8,9的版本都更换过,问题依然.但是有网友确实可以解决,但是更换到具体什么版本未知.

2.前端http请求的时候对参数进行URL编码处理,理论上是绝对可行的,但是已有的http请求数很多,一个一个修改工作量大.未试

3.配置tomcat的catalina.properties 添加或者修改： tomcat.util.http.parser.HttpParser.requestTargetAllow=|{}

4.使用Connector中relaxedPathChars和relaxedQueryChars属性可以解决问题.找到tomcat/conf/server.xml,在Connector中增加这两个配置.
`<Connector port="8080" protocol="HTTP/1.1"    relaxedPathChars="[]{}|\^" relaxedQueryChars="[]{}|\^" />`