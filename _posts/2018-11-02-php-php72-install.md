---
layout: post
title: 'PHP:PHP7.2安装'
date: 2018-11-01
categories: PHP
tags: PHP
---

通过yum安装php7.2

rpm安装php7.2相应的yum源

>rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm


用下面命令查看yum所拥有版本的各个插件

>yum list php*


安装php7.2，也可以日后看需要什么就可以单独安装插件。

>yum install php72w php72w-opoache php72--cli  php72w-devel