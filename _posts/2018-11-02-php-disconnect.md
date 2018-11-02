---
layout: post
title: 'PHP:主动断缆与浏览器的连接'
date: 2018-11-01
categories: PHP
tags: PHP
---

```php
$size = ob_get_length();  
header("Content-Length: $size");  //告诉浏览器数据长度,浏览器接收到此长度数据后就不再接收数据
header("Connection: Close");      //告诉浏览器关闭当前连接,即为短连接
ob_flush();  
flush();  
//ob_flush是把在PHP緩衝區(output_buffer)(假設有打開)的東西輸出，但並不是立刻輸出到螢幕上
//flush則是把非PHP緩衝區，伺服器上準備輸出的資料輸出到瀏覽器上"顯示出來"
//因此順序上必須先用ob_flush()把緩衝區上的資料輸出後，才能用flush()把從緩衝區輸出的資料給列印到螢
//幕上，不然光用ob_flush()，把緩衝區清空他並不會立刻把資料輸出到螢幕上
//而只用flush()，資料全部在緩衝區他也沒東西可列印到螢幕上
```