---
layout: post
title: 'PHP:修改上传文件限制'
date: 2018-11-02
categories: PHP
tags: PHP
author: 李昕
cover: 'https://lixin.blog/assets/img/php_banner.jpg'
---

打开php.ini，首先找到

file_uploads = on ;是否允许通过HTTP上传文件的开关。默认为ON即是开

upload_tmp_dir ;文件上传至服务器上存储临时文件的地方，如果没指定就会用系统默认的临时文件夹

upload_max_filesize = 8m ;望文生意，即允许上传文件大小的最大值。默认为2M

post_max_size = 8m ;指通过表单POST给PHP的所能接收的最大值，包括表单里的所有值。默认为8M

一般地，设置好上述四个参数后，上传<=8M的文件是不成问题，在网络正常的情况下。

但如果要上传>8M的大体积文件，只设置上述四项还一定能行的通。

进一步配置以下的参数

max_execution_time = 600 ;每个PHP页面运行的最大时间值(秒)，默认30秒

max_input_time = 600 ;每个PHP页面接收数据所需的最大时间，默认60秒

memory_limit = 8m ;每个PHP页面所吃掉的最大内存，默认8M

把上述参数修改后，在网络所允许的正常情况下，就可以上传大体积文件了

>max_execution_time = 600
max_input_time = 600
memory_limit = 32m
file_uploads = on
upload_tmp_dir = /tmp
upload_max_filesize = 32m
post_max_size = 32m

Tips:

使用 ini_set 方法是不能修改 post_max_size、upload_max_filesize的

ini_set 可以修改的范围见http://php.net/manual/zh/ini.list.php

如果想在某个目录下修改php配置，使用.htaccess文件

httpd.conf 要配置 AllowOverride All

>php_value upload_max_filesize 50M
php_value post_max_size 100M



