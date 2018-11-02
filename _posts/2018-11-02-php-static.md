---
layout: post
title: 'PHP:static关键词修饰方法内变量'
date: 2018-11-01
categories: PHP
tags: PHP
---

用static 修饰的方法内变量，会改变变量的生命周期，但不改变变量的作用域；

```php
function test1(){
    static $test = 0; 
    echo ++$test. "\n";
}
test1();
test1();
test1();
test1();
//输出
//1
//2
//3
//4
```