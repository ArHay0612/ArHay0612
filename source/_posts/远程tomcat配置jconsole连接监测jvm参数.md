---
title: 远程tomcat配置jconsole连接监测jvm参数
date: 2018-08-20 11:10:30
description:       jconsole是Java 自带性能监控工具，监视和管理控制台 jconsole，它可以提供 Java 某个进程的内存、线程、类加载、
      jvm 概要以及 MBean 等的实时信息.
      Jvisualvm是jdk1.6 update 7 才有，是jconsole的升级工具，功能更强大，最大好处是支持插件安装。所以Jvisualvm远程
      JMX连接方式和jconsole远程连接方式一样
tags:
  - java
  - jconsole
  - jvm
  - tomcat
---

1.在tomcat的bin目录下catalina.sh文件首部增加以下配置(注意:不用换行)

    CATALINA_OPTS="-Dfile.encoding=UTF-8 -server -Xms256m -Xmx256m -Djava.rmi.server.hostname=0.0.0.0 -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=10001 -Dcom.sun.management.jmxremote.rmi.port=10001 -Dcom.sun.management.jmxremote.authenticate=true -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.password.file=./conf/jmxremote.password -Dcom.sun.management.jmxremote.access.file=./conf/jmxremote.access"

    其中-Xms256m -Xmx256m是配置jvm虚拟机参数的,最小堆内存和最大堆内存,推荐保持一致,如果不一致会增加gc回收次数,
    对性能有严重影响.
    -Djava.rmi.server.hostname:本机的ip地址,可设置为0.0.0.0
    -Dcom.sun.management.jmxremote.port:监控的端口.不能和其他运行的端口相冲突
    -Dcom.sun.management.jmxremote.authenticate:需要授权才能进行连接
    -Dcom.sun.management.jmxremote.password.file:指定配置授权的密码文件存放位置,推荐放入到tomcat的conf目录下
    -Dcom.sun.management.jmxremote.access.file:指定配置授权账户的权限的文件存放位置,推荐放入到tomcat的conf目录下


2.在tomcat的bin目录下startup.sh文件首部增加以下配置

    JAVA_OPTS="-Djava.rmi.server.hostname=0.0.0.0"

3.启动本地的jconsole即可,输入远程ip和端口,username和password即可连接

注意:

    1.配置授权的2个文件是在系统的%JAVA_HOME%/jre/lib/management目录下可以找到对应的模板.将其复制到tomcat的conf目录下,
    并将jmxremote.password.template重命名为jmxremote.password
    2.jmxremote.access用户权限分readonly和readwrite两种，在jmxremote.access尾部添加用户权限"admin  readwrite",
    其中admin代表远程授权的用户名
    3.在jmxremote.password尾部添加用户密码"admin 123456"其中admin代表用户名,123456代表对应的密码
    4.对jmxremote.access和jmxremote.password文件进行授权,chmod 600  jmxremote.access和
    chmod 600 jmxremote.password
    5.针对为什么在startup.sh文件中增加对应的-Djava.rmi.server.hostname=0.0.0.0配置,主要是因为在不加配置的情况下,
    用shutdown.sh关闭tomcat的时候会报该端口已经被占用,因为关闭tomcat时候，还会读取catalina.sh.所以推荐在startup.sh文件中配置
    6.一般情况下远程服务器系统是开启防火墙的,所以还需要将10001端口配置为允许访问
    7.如果配置一切无误,还是连接不上的话,请将0.0.0.0换成对应的IP地址.因为亲测在Ubuntu下0.0.0.0能连接成功,
    但是在centos7下连接不成功

