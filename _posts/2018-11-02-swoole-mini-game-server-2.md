---
layout: post
title: 'Swoole:从头做一个微信小游戏服务器（二）'
date: 2018-11-02
categories: Swoole
tags: Swoole
author: 李昕
---

根据官方文档，swoole仅支持 Linux、FreeBSD、MacOS 三种操作系统，对于windows来说需要跑虚拟机或者Linux子系统（windows10）再或者是docker上。
正好我手边有一台centos7的服务器，下面所有的配置都是基于centos7来说的。

**首先安装php7**

之前的文章有记录如何[安装php72](https://lixin.blog/2018/11/01/php-php72-install.html)

**安装swoole**

先安装git

>yum install git

克隆项目到指定文件夹

>git clone https://github.com/swoole/swoole-src.git /home/swoole

编译swoole项目

>phpize
./configure --enable-openssl
make && make install

出现了如下错误：

>...
error: C++ preprocessor "/lib/cpp" fails sanity check 
...

问题的根源是缺少必要的C++库。如果是CentOS系统，运行，如下命令解决：

>yum install glibc-headers
yum install gcc-c++
 
查看swoole.so文件，路径是

>/usr/lib64/php/modules

如果生成了就修改php.ini文件

修改php.ini文件，添加

>extension=swoole.so

检查是否成功，执行php -m

如果有swoole模块就说明安装成功