---
title: Linux下离线安装nodejs
date: 2018-05-12 18:36:16
description: Linux下离线安装nodejs
tags:
- linux
- nodejs
---


##### Linux下离线安装nodejs步骤:
---

1.在官网下载Linux版本的nodejs安装包,然后解压并且移动到/usr/local/目录,具体目录可随意指定

`sudo tar -xvJf ./node-v8.9.3-linux-x64.tar.xz -C /usr/local/`

2.配置环境变量，编辑～/.bashrc文件(当前用户的环境变量配置)

`export NODE_HOME=/opt/node`

`export PATH=$PATH:$NODE_HOME/bin`

`export NODE_PATH=$NODE_HOME/lib/node_modules`

3.使配置生效,执行source命令

`source ~/.bashrc`

4.执行如下命令校验配置是否生效

`node -v`

`npm -v`
