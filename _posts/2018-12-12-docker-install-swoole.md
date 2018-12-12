---
layout: post
title: "Docker:安装和启动swoole项目"
date: 2018-12-12
categories: Docker
tags: Docker
author: 李昕
cover: "https://lixin.blog/assets/img/docker_banner.png"
---

由于项目扩展，需要部署一个轻量的消息队列服务器，打算用 swoole 撸一个看看效果。

首先解决部署问题，这次计划把服务器部署在 docker 上，docker 的好处就不必说了。

docker 镜像采用的是 twose/swoole-docker

 官方的介绍

> Introduction
>
> 基于最新 PHP7.2-cli 版本
>
> 使用 swoole1.X~4.X 最新版本构建, 所有功能火力全开
>
> 提供 Swoole 的绝佳搭档: MySQL, Redis, Inotify, 配合 docker-compose, 实现开箱即用
>
> 已安装 ["GD", "iconv", "pdo_mysql", "dom", "xml", "curl", "swoole"]等 PHP 扩展
>
> 已开启["coroutine", "openssl", "http2", "async-redis", "mysqlnd", "swoole-serialize"]等所有功能
>
> 纯环境 , 0 冗余 , 绿色清洁 , 无任何 php 代码
>
> 默认中国上海时区
>
> inotify 提供自动化热更新等支持

看起来蛮厉害的

项目地址[点这里](https://github.com/twose/swoole-docker)

我们开始最基本 swoole 环境的部署

docker 拉取  镜像

> docker pull twosee/swoole-coroutine

查看一下 php 版本

> \# docker run -it --rm twosee/swoole-coroutine bash
>
> \# php -v
>
> PHP 7.2.12 (cli) (built: Nov 16 2018 03:17:59) ( NTS )
>
> Copyright (c) 1997-2018 The PHP Group
>
> Zend Engine v3.2.0, Copyright (c) 1998-2018 Zend Technologies
> with Zend OPcache v7.2.12, Copyright (c) 1999-2018, by Zend
> Technologies

能看到是 php7.2

查看一下模块

```
[PHP Modules]
Core
ctype
curl
date
dom
fileinfo
filter
ftp
gd
hash
iconv
json
libxml
mbstring
mysqli
mysqlnd
openssl
pcntl
pcre
PDO
pdo_mysql
pdo_sqlite
Phar
posix
readline
redis
Reflection
session
SimpleXML
sockets
sodium
SPL
sqlite3
standard
swoole
tokenizer
xml
xmlreader
xmlwriter
Zend OPcache
zlib

[Zend Modules]
Zend OPcache
```

 已经加载了 swoole

启动服务器

现在宿主机目录里面创建一个 server.php 的文件，代码部分就用 swoole http 服务器官方  例子就行

 执行 docker run

> docker run -d \\
>
> --name=swoole \\
>
> -v \$(pwd):/home/www \\
>
> -p 9501:9501 \\
>
> twosee/swoole-coroutine \\
>
> php /home/www/server.php start

服务器启动成功
