---
title: deepin linux下初识docker
date: 2019-05-29 10:27:29
description: deepin linux 安装最新版 docker
tags:
  - linux
  - docker
---

### deepin linux 安装最新版 docker

可以参考官放 wiki 文档进行安装,地址如下:

https://wiki.deepin.org/wiki/Docker#.E5.9C.A8_Deepin_.E4.B8.AD.E5.AE.89.E8.A3.85_Docker_.E6.9C.80.E6.96.B0.E7.89.88.E7.9A.84.E6.96.B9.E6.B3.95

但官网打开速度比较慢,另外关于最后一项禁止开启自启官方说的方式是无效的,笔者亲试至少在(deepin 15.10.1 基于 unstable 升级上来的)是无效的.所以将详情步骤记录如下:

    注:执行apt命令之前,最好先执行一次更新仓库操作sudo apt update

1.如果以前安装过老版本，要确保先卸载以前版本.

`sudo apt remove docker.io docker-engine`

2.安装密钥管理与下载相关的工具

// 密钥管理（add-apt-repository ca-certificates 等）与下载（curl 等）相关的工具

`sudo apt-get install apt-transport-https ca-certificates curl python-software-properties software-properties-common`

3.下载并安装密钥

`curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -`

执行成功后返回 OK 即可.如果不成功的话,可能是网络问题,我这儿是处于翻墙状态,所以是能成功的.不能成功的话,可以按照官方 wiki 上说的使用国内镜像源`curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/debian/gpg | sudo apt-key add -`

4.查看密钥是否安装成功

`sudo apt-get fingerprint 0EBFCD88`

如果成功会提示

`pub 4096R/0EBFCD88 2017-02-22 Key fingerprint = 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88`
`uid Docker Release (CE deb) <docker@docker.com>`
`sub 4096R/F273FCD8 2017-02-22`

5.在 source.list 中添加 docker-ce 软件源

    在此需要注意当前系统版本,执行 cat /etc/debian_version查看当前系统是基于debian的哪个版本.debian版本号和系统代号如下:

| 系统代号 | 版本号 |
| -------- | ------ |
| squeeze  | 6.x    |
| wheezy   | 7.x    |
| jessie   | 8.x    |
| stretch  | 9.x    |

deepin 15.10.x 是基于 debian9.0 的,所以加入源如下:

sudo vim /etc/apt/sources.list

`deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch stable`

6.更新仓库

`sudo apt update`

7.安装 docker-ce

`sudo apt install docker-ce`

    注:这一步网络不好可能会导致失败,多试几次总会成功的.

8.启动 docker

`sudo systemctl start docker` 或者
`service docker start`

9.查看安装的版本信息

`docker version`

10.验证 docker 是否被正确安装并且能够正常使用

`sudo docker run hello-world`

如果能够正常下载，并能够正常执行，则说明 docker 正常安装

11.让普通用户也能运行 docker

    默认情况下，普通用户运行 docker 会有权限问题，每次运行都得加 sudo，很麻烦。把你的账号加到 docker 用户组后就不用加 sudo 了：

`sudo usermod -aG docker test` // test 是用户名,替换为自己的,执行后注销登录

12.docker service 默认是开机自启的,强迫症取消开机自启的

    这一点,官方说的安装chkconfig来管理

安装 chkconfig

`sudo apt install chkconfig`

移除自启

`sudo chkconfig --del docker`

    但是试了,重启无效无效.需要通过systemctl命令来禁止

`sudo systemctl disable docker`

### docker 使用初识

#### docker 入门命令

docker 安装后,默认是没有任何镜像的,如果安装后执行了 docker run hello-world 的话,是有一个 hello-world 的镜像的.

`docker images` // 查看本地的镜像

可以通过 pull 命令获取相关镜像

`docker search nginx` // 在 docker.io 上搜索 nginx 相关的镜像

`docker pull nginx:latest` // latest 代表取最新版本,要获取其他版本 docker pull nginx:xxxx

`docker run -itd --name nginx1.0 nginx` // -d: 后台启动容器;-i: 以交互模式运行容器，通常与 -t 同时使用;-t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用; --name：容器的别名，默认为随机的,这儿为 nginx1.0

    第一次使用run运行指定别名后,以后可通过docker start+ 别名的方式启动

`docker ps` // 查看正在运行的容器

`docker ps -a` // 查看所有容器

`docker ps -l` // 查看最近一次运行的容器

`docker exec -it nginx1.0 bash` // 进入 nginx1.0 容器的命令行

`docker start nginx1.0` // 启动 nginx1.0 容器

`docker stop nginx1.0` // 停止 nginx1.0 容器

`docker rm nginx1.0` // 删除 nginx1.0 容器

#### docker 网络

linux 使用 namespace 来进行资源的隔离 ，docker 的隔离性

1.docker 的网路类型分为：

Bridge 模式：桥接（默认的模式）

host 模式：容器将不会获得独立的 network namespace，将和主机公用一个；即在 docker 中使用网络和主机上一样的；

None：不与外界任何东西进行通讯

2.采用 Bridge 的时候需要和主机通讯，就需要使用端口映射

docker run -d --name nginx1.0 -p 8080:80 nginx # 主机的 8080 端口映射到容器中的 80 端口

    多个端口映射可以跟多个-p,比如:-p 8080:80 -p 6379:6379

#### docker 镜像备份和导入镜像

`docker save -o /home/xxx/images/nginx.tar nginx1.0` // 将 nginx1.0 镜像备份到/home/xxx/images/目录下

`docker load --input /home/xxx/images/nginx.tar` // 导入镜像

#### docker挂载物理机本地目录

docker可以支持把一个宿主机上的目录挂载到镜像里。

`docker run -itd -v /home/bz/Downloads:/home/Downloads nginx1.0` // 通过-v参数，冒号前为宿主机目录，必须为绝对路径，冒号后为镜像内挂载的路径

默认挂载的路径权限为读写。如果指定为只读可以用：ro

`docker run -itd -v /home/bz/Downloads:/home/Downloads:ro nginx1.0`