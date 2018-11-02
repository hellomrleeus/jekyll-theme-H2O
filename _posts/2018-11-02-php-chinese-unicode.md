---
layout: post
title: 'PHP:unicode转中文'
date: 2018-11-02
categories: PHP
tags: PHP
author: 李昕
---

方法一

```php
$str = preg_replace("#\\\u([0-9a-f]{4})#ie", "iconv('UCS-2BE', 'UTF-8', pack('H4', '\\1'))", $str);
```

方法二(适用于没有换行符的情况)

```php
json_decode('{"str":"$str"}', true)['str']
```
