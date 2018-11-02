---
layout: post
title: 'PHP:生成uuid'
date: 2018-11-02
categories: PHP
tags: PHP
author: 李昕
cover: 'https://lixin.blog/assets/img/php_banner.png'
---

UUID是 通用唯一识别码（Universally Unique Identifier），可以在大多数场景中用于表示唯一ID

```php
//可以自定义分隔符
 function uuid($separator = '-') {
    $chars = md5(uniqid(mt_rand(), true));
    $uuid  = substr($chars,0,8) . $separator;
    $uuid .= substr($chars,8,4) . $separator;
    $uuid .= substr($chars,12,4) . $separator;
    $uuid .= substr($chars,16,4) . $separator;
    $uuid .= substr($chars,20,12);
    return . $uuid;
}  
```