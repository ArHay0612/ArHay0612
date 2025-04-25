---
layout: linux离线安装oracle JDK
title: linux离线安装oracle JDK
date: 2018-05-13 11:02:48
description: 本文是在deepin linux(基于debian发行版)系统环境下,debian,ubuntu以及其他debian衍生版同理适用
tags:
  - linux
  - java
  - jdk
---

    摘要:
      本文是在deepin linux(基于debian发行版)系统环境下,debian,ubuntu以及其他debian衍生版同理适用

如果电脑处于联网状态,那么可以使用apt包管理器在线安装,可使用以下命令在线安装:

`sudo apt update #更新`

`sudo apt install oracle-java8 #安装`

 下面重点介绍离线安装官网下载安装包的方式:

 1.oracle官网下载Linux对应的tar.gz安装包

 2.进入到存放安装包的目录下,执行以下命令将安装包的内容解压到在指定目录(/usr/local/java/文件夹自己事先建好)

 `sudo tar zxvf ./xxxx.tar.gz  -C /usr/local/java`

3.查看第二步是否成功,如果有/usr/local/java/下有jdk对应的目录结构,则表示成功

`ls -anl /usr/local/java/`

4.配置环境变量(此处配置到当前用户的环境变量上)

`sudo  vim ~/.bashrc #用vim打开当前用户的环境变量配置文件`

在.bashrc文件底部加入以下内容,然后保存退出

    export JAVA_HOME=/usr/local/java/java-8u5_xxx

    export JRE_HOME=${JAVA_HOME}/jre   

    export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib   

    export PATH=${JAVA_HOME}/bin:$PATH

    unset _JAVA_OPTIONS



5.执行以下命令使刚刚的配置生效

`source ~/.bashrc`

6.验证安装结果,执行下列命令,如果一切无误,会正常出现对应的java版本信息

`java -version`
