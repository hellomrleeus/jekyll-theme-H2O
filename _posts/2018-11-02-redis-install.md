---
layout: post
title: 'Redis:Linux安装Redis'
date: 2018-11-02
categories: Redis
tags: Redis
author: 李昕
cover: 'https://lixin.blog/assets/img/redis_banner.png'
---

1. 下载

cd到下载目录，下载

>wget http://pecl.php.net/get/redis-3.1.2.tgz

2. 解压

解压到当前目录 

>tar -zxvf redis-3.1.2.tgz

3. 安装

cd redis-3.1.2目录下

在该目录下用phpize生成configure配置文件：直接运行

>/usr/local/php7/bin/phpize

然后就是配置、编译、安装全部在该目录下完成

>./configure --with-php-config=/usr/bin/php-config
make
make install
make install

后会看到

>Installing shared extensions:     /usr/lib64/php/modules/

该目录就是redis.so文件的生成目录

看到redis.so就说明安装成功了

4. 配置支持PHP7：

添加extension=redis.so