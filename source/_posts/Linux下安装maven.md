---
title: Linux下安装maven
date: 2018-05-13 11:18:57
description: Linux系统下离线安装maven
tags:
  - java
  - linux
  - maven
---

    摘要:
      Linux系统下离线安装maven

1.在Apache官方网站下载对应系统的maven包然后解压maven

2.配置环境变量.(配置到当前用户的环境变量上)

`sudo  vim ~/.bashrc #用vim打开当前用户的环境变量配置文件`

在.bashrc文件底部加入以下内容,然后保存退出.(M2_HOME代表解压后的maven目录)

    export M2_HOME=/home/user/apache-maven-3.3.9

    export PATH=${M2_HOME}/bin:$PATH

3.执行以下命令使刚刚的配置生效

    `source ~/.bashrc`

4.验证安装结果,执行下列命令,如果一切无误,会正常出现对应的maven版本信息

    `mvn -v`
