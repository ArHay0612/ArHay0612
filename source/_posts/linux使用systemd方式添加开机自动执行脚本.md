---
title: linux使用systemd方式添加开机自动执行脚本
date: 2019-06-26 10:31:19
description: linux使用systemd方式添加开机自动执行脚本
tags:
  - linux
---
  
>前段时间在一台公网服务器上搭建了vpn服务用来映射内网一台gitlab服务器,实现跨网络也能进行代码提交等操作.过程中经过查找网络上的博客文档基本都没啥问题,但是后续使用过程中,发现客户端(pptp-linux)vpn连接会自动断开(大约是晚上的时候),后面写了一个脚本后台常驻,检测vpn是否连接,如果断开则自动重连.但是问题来了,如果服务器关机了然后开机,则不会自动去连接,需要手工执行连接的脚本.于是,查找相关的systemd方式实现开机启动执行相关脚本.由于内网服务器默认登录的账户不是root身份,所以使用其他小伙伴的建立/etc/rc.local文件的方式是未成功.以下是测试能够通过的方式(我的内网gitlab服务器是Ubuntu18.04.1-server版,理论上只要使用systemd的方式来管理系统服务启动的发行版都可以)

### 准备好要执行的脚本文件(auto_conn.sh)

```bash
  #! /bin/sh
  while true
  pppdNum=`ifconfig | grep ppp0 | wc -l`
  do echo "pppdNum = $pppdNum"
  if [ $pppdNum -le 0 ]
  then
        # 
        echo "vpn is down,waitting for connectting again..."
        sleep 10
        pppdNum_1=`ifconfig | grep ppp0 | wc -l`
        echo "pppdNum = $pppdNum_1"
        #
        if [ $pppdNum_1 -ge 1 ]
        then
            echo "vpn has autolly connect success again!"
            # xxxxx是sudo执行的密码,每次连接后需要手工添加路由表,不然不能访问到服务器,ppp0是该网卡的名称.可通过ifconfig查看192.168.2.0/24是自己外网vpn服务器给内部电脑分配的内网ip网关前缀
            echo 'xxxxx' | sudo -S route add -net 192.168.2.0/24 ppp0
        else
            echo "connectting.."
            # xxxxx是sudo执行的密码,vpn_name是自定义vpn连接的名称,000.000.000.000是vpn服务器的ip(公网ip),username是vpn登录的用户名,passwd是vpn登录的密码
            echo 'xxxxxx' | sudo -S pptpsetup --create vpn_name --server 000.000.000.000 --username username --password passwd vpn-only --encrypt --start
            echo 'c vpn_client' > /var/run/xl2tpd/l2tp-control
                sleep 10
            # xxxxx是sudo执行的密码,每次连接后需要手工添加路由表,不然不能访问到服务器,ppp0是该网卡的名称.可通过ifconfig查看192.168.2.0/24是自己外网vpn服务器给内部电脑分配的内网ip网关前缀
            echo 'xxxxxx' | sudo -S route add -net 192.168.2.0/24 ppp0
        fi
  fi
  sleep 5

  done
```
    注: 某条命令需要sudo执行的话,在脚本中可使用echo 'xxxxxx' | sudo -S 的方式,xxxxxx就是对应的密码

然后给脚本添加执行权限.sudo chmod +x

### 创建一个service文件

`sudo vim /etc/systemd/system/auto_startVPN.service`

详细内容如下:

```bash
[Unit]
Description=自动连接vpn #自定义的简介描述
After=network-online.target.wants #脚本所需要的前置service，可在/etc/systemd/system/下查看

[Service]
ExecStart=/home/xxx/xxx/auto_conn.sh #第一步中的脚本文件路径

[Install]
WantedBy=multi-user.target

```
service文件一般正常的启动文件主要分成三部分

[Unit] 段: 启动顺序与依赖关系

[Service] 段: 启动行为,如何启动，启动类型

[Install] 段: 定义如何安装这个配置文件，即怎样做到开机启动

### 使用systemctl命令使能这个服务开机启动

`sudo systemctl daemon-reload` //重新加载配置文件

`sudo systemctl enable auto_startVPN.service` //设置开机启动刚刚新建的自动连接vpn的服务

重启电脑,等待个大约10多秒,执行ifconfig,会发现连接中会有ppp0这个网卡设备和对应的ip地址等信息,说明脚本执行成功也成功的自动连接上了vpn服务器.