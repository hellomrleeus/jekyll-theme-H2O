---
layout: post
title: 'PHP:文件上传、获取大小、获取MD5'
date: 2018-11-02
categories: PHP
tags: PHP
author: 李昕
cover: 'https://lixin.blog/assets/img/php_banner.jpg'
---

![](https://lixin.blog/assets/post_img/php_upload_img_1.png)

$_FILES['img']['error']有以下几种类型

1、UPLOAD_ERR_OK

其值为 0，没有错误发生，文件上传成功。
 
2、UPLOAD_ERR_INI_SIZE

其值为 1，上传的文件超过了 php.ini 中 upload_max_filesize选项限制的值。
 
3、UPLOAD_ERR_FORM_SIZE

其值为 2，上传文件的大小超过了 HTML 表单中 MAX_FILE_SIZE 选项指定的值。
 
4、UPLOAD_ERR_PARTIAL

其值为 3，文件只有部分被上传。
 
5、UPLOAD_ERR_NO_FILE

其值为 4，没有文件被上传。
 
6、UPLOAD_ERR_NO_TMP_DIR

其值为 6，找不到临时文件夹。PHP 4.3.10 和 PHP 5.0.3 引进。
 
7、UPLOAD_ERR_CANT_WRITE

其值为 7，文件写入失败。PHP 5.1.0 引进。 

转移文件到自定目录

move_uploaded_file($fileinfo['tmp_name'],$fileinfo['name']);

*这种方式只对上传的文件有效，如果尝试从A文件夹移动到B文件夹，这个函数没有效果并且不会报错 

文件夹之间转移文件使用函数rename

获取文件大小

```php
$arrUpdateInfo['size'] = filesize($sFileName);
```

获取文件MD5

```php
$filename = "test.txt";
$md5file = md5_file($filename);
echo $md5file;
```