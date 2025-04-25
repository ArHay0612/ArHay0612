---
title: linux系统之间远程连接和传输文件
date: 2018-08-07 10:06:41
description: linux系统之间远程连接和传输文件
tags: linux
---

    >摘要: 
            
    1.Windows与Windows系统之间可以开启远程桌面连接实现远程控制和传输文件,也可以搭建ftp服务器然后通过ftp客户端
    (例如:fileZilla)来实现.
    2.从Windows到Linux,可以使用xshell等工具来实现远程连接.然后使用winscp等工具实现文件传输.当然在Linux上
    搭建ftp服务器,然后用ftp客户端连接也可
    3.从Linux(桌面版,server版一般没必要)到Windows.可以使用remmina工具来远程连接Windows进行连接操作.
    文件拷贝推荐在Windows上搭建ftpserver(fileZilla-server),然后使用fileZilla client连接传输文件.

     注:fileZilla跨平台的.占用资源少,操作方便.简直神器~
     
    4.另外一种方式就是不分双方的操作系统.只要在对应的机器上安装对应的一些工具,比如teamviewer等.也可实现远程连接
    和文件传输.
    5.Linux到Linux之间通常是桌面端到server端的操作,有时候为了方便或者为了减少服务器资源占用,server端不需要安装
    额外的一些工具等,这时推荐使用Linux下的ssh和scp命令进行操作(大力推荐~)

## 1.ssh命令

  简单说，SSH是一种网络协议，用于计算机之间的加密登录。如果一个用户从本地计算机，使用SSH协议登录另一台远程计算机，我们就可以认为，这种登录是安全的，即使被中途截获，密码也不会泄露.SSH只是一种协议，存在多种实现，既有商业实现，也有开源实现。本文针对的实现是OpenSSH(linux一般自带)，它是自由软件，应用非常广泛.注:ssh协议默认端口一般是22
  
    ssh -V 查看当前安装的ssh版本

1.1 连接到远程主机方式1
    
    ssh username@serverAddress username是登录远程主机的用户名,serverAddress远程主机地址

1.2 连接到远程主机方式2

    ssh serverAddress -l username -p 22 serverAddress远程主机地址,可以是一个域名地址或者ip地址,
    username是登录远程主机的用户名, -p 指定远程服务端ssh协议开放的端口.

## 2.scp命令

    scp用于实现在Linux server端和Linux客户端实现文件传输

2.1 上传文件到服务器端,注:是文件,不是文件夹

    scp ./test.js root@192.168.1.106:/var/www/ #将本地当前目录下的test.js文件上传到192.168.1.106的
    /var/www/目录下.root登录远程及其的用户名.

2.2 上传文件夹(目录)到服务器端

    scp -r ./test/ root@192.168.1.106:/var/www/ #将本地当前目录下的test目录上传到192.168.1.106的/var/www/目录下.
    -r参数代表上传目录

2.3 从服务器上下载文件

    scp root@192.168.1.106:/var/www/test.js /home/ #将服务器/var/www/目录下的test.js文件下载到本地的home目录下

2.4 从服务器下载整个目录

    scp -r root@192.168.1.106:/var/www/test/ /home/ #将服务器上的/var/www/test/目录下载到本地的home目录下
    -r参数代表目录