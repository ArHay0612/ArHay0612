---
title: Linux常用压缩解压缩
date: 2018-05-20 17:08:52
tags: linux
description: Linux常用压缩解压缩
---

    摘要:
      Linux下常用的压缩格式有:.zip .gz .bz2 .tar.gz .tar.bz2

#### 1 .zip格式压缩

压缩文件 `zip 压缩文件名 源文件`

    zip test.zip ./test.txt #将当前目录的test.txt文档压缩为test.zip

压缩目录 `zip -r 压缩文件名 源目录`

    zip test1.zip ./test1 #将当前目录的test1目录压缩为test1.zip

#### 2 .zip格式解压缩

解压文件  `unzip 压缩文件`

    zip ./test1.zip #解压缩当前目录的test1.zip文件

#### 3 .gz格式压缩

压缩为.gz文件,源文件消失  `gzip 源文件`

压缩为.gz文件,源文件保留  `gzip -c 源文件 > 压缩文件`

压缩目录下的所有子文件,但是不能压缩目录  `gzip -r 目录`

#### 4 .gz格式解压缩

解压缩.gz文件  `gzip -d 压缩文件`

解压缩.gz文件  `gunzip  压缩文件`

#### 5 .bz2格式压缩

压缩为.bz2格式,不保留源文件  `bzip2 源文件`

压缩为.bz2格式,保留源文件 `bzip2 -k 源文件`

注:bzip2不能压缩目录

#### 6 .bz2格式解压缩

解压缩.bz2文件,-k保留压缩文件  `bzip2 -d 压缩文件`

解压缩.bz2文件,-k保留压缩文件  `bunzip2 压缩文件`

#### 7 .tar.gz格式压缩(先打包为tar,再压缩为.gz文件)

tar打包命令  `tar -cvf 打包文件名 源文件`

选项: -c 打包; -v 现实过程; -f 指定打包后的文件名

tar -cvf test.tar ./test 将当前目录下test目录打包为tar文件

gzip test.tar  生成test.tar.gz文件

bzip2 test.tar 生成test.tar.bz2文件

上述过程繁琐,可以直接用  `tar -zcvf 压缩包名.tar.gz 源文件`

#### 8 .tar.gz格式解压缩(先用gzip解压文件,然后解打包)

解打包命令  `tar -xvf 打包文件名`

选项: -x 解打包

tar -vxf test.tar   解包text.tar文件

上述过程繁琐,可以直接用  `tar -zxvf 压缩包名.tar.gz`,
解压到指定目录可用-C选项指定目录 `tar -zxvf 压缩包名.tar.gz -C /tmp/`

#### 9 .tar.bz2格式压缩(先打包为tar,再压缩为.bz2文件)

tar打包命令  `tar -cvf 打包文件名 源文件`

选项: -c 打包; -v 现实过程; -f 指定打包后的文件名

tar -cvf test.tar ./test 将当前目录下test目录打包为tar文件

bzip2 test.tar 生成test.tar.bz2文件

上述过程繁琐,可以直接用  `tar -jcvf 压缩包名.tar.bz2 源文件`

#### 10 .tar.bz2格式解压缩(先用bzip2解压文件,然后解打包)

解打包命令  `tar -xvf 打包文件名`

选项: -x 解打包

tar -vxf test.tar   解包text.tar文件

上述过程繁琐,可以直接用  `tar -jxvf 压缩包名.tar.bz2`,
解压到指定目录可用-C选项指定目录 `tar -jxvf 压缩包名.tar.bz2 -C /tmp/`
