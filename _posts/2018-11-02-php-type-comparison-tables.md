---
layout: post
title: 'PHP:类型比较表'
date: 2018-11-02
categories: PHP
tags: PHP
author: 李昕
cover: 'https://lixin.blog/assets/img/php_banner.jpg'
---

![](https://lixin.blog/assets/post_img/php_type_img_1.png)

![](https://lixin.blog/assets/post_img/php_type_img_2.png)

![](https://lixin.blog/assets/post_img/php_type_img_3.png)

Note:

HTML 表单并不传递整数、浮点数或者布尔值，它们只传递字符串。要想检测一个字符串是不是数字，可以使用 is_numeric() 函数。


在没有定义变量 \$x 的时候，诸如 if (\$x) 的用法会导致一个 E_NOTICE 级别的错误。所以，可以考虑用 empty() 或者 isset() 函数来初始化变量。

