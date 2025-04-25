---
title: Linux增加使用文件代替swap分区分方法
date: 2018-05-13 10:52:59
description: Linux增加使用文件代替swap分区
tags:
  - linux
  - swap
---

    摘要:
      在安装Linux系统的时候未对系统进行swap(交换分区),后续进入系统
      可以采用新建文件的方式来代替swap分区.
      以下所执行的系统环境是:deepin linux(基于debian发行版),
      按理在Ubuntu,debian上也是可以的.

注意:执行以下命令时,全部采用root账户的权限

1.创建要作为swap分区的文件:增加1GB大小的交换分区，则命令写法如下，其中的count等于想要的块的数量（bs*count=文件大小）

`sudo dd if=/dev/zero of=/swapfile bs=1M count=1024`

2.格式化为交换分区文件,建立swap的文件系统

`sudo mkswap /swapfile`

3.启用交换分区文件

`sudo swapon /swapfile`

4.使系统开机时自启用，在文件/etc/fstab中添加一行(可使用vim打开文件进行编辑)：

`/swapfile swap swap defaults 0 0`

5.验证结果,执行free 命令查看是否有交换分区

`free -m `

注:如果想移除swap分区文件,执行以下命令:

`sudo swapoff /swapfile && sudo rm /swapfile`
