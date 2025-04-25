---
title: debian系系统编译CuraEngine引擎
date: 2018-09-05 16:13:22
description: CuraEngine是一个功能强大、快速、强劲的3D模型切片引擎.Cura就是采用了CuraEngine引擎的.
tags: linux
---

      摘要:CuraEngine是一个功能强大、快速、强劲的3D模型切片引擎.Cura就是采用了CuraEngine引擎的.
      本文的操作环境为deepin 15.7,编译CuraEngine的版本为2.4

1.安装cmake

    sudo apt install cmake

2.安装Protobuf >= 3.0.0 
  
  2.1 安装libtool

    sudo apt install libtool
  
  2.2 安装autoconf
  
    sudo apt install autoconf
  2.3 clone代码,--depth=1.clone最近一次提交的,可以减少clone时间
  
    git clone https://github.com/protocolbuffers/protobuf.git --depth=1

  2.4 进入到protobuf目录.执行
  
    ./autogen.sh

  2.5 
  
    ./configure
    
  2.6 
  
    make 

  2.7 
  
    sudo make install

3.安装libArcus

  3.1 安装python3-dev
      
      sudo apt install python3-dev
  
  3.2 安装python3-sip-dev
  
    sudo apt install python3-sip-dev

  3.3 安装libprotobuf-dev
  
    sudo apt install libprotobuf-dev

  3.4 clone代码
  
    git clone https://github.com/Ultimaker/libArcus.git --depth=1

  3.5 进入到libArcus目录,执行

    mkdir build && cd build
    cmake ..

  3.6 
  
    make

  3.7 
  
    sudo make install

4.编译CuraEngine

  4.1 clone代码.此处编译2.4版本,-b指定版本
  
    git clone https://github.com/Ultimaker/CuraEngine.git -b 2.4 --depth=1
    
  4.2 
  
    mkdir build && cd build

  4.3 
  
    cmake ..


  4.4 
  
    make