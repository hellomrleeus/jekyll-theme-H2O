---
layout: post
title: "PHP:Laravel学习笔记 Eloquent ORM"
date: 2019-01-10
categories: PHP
tags: PHP
author: 李昕
cover: "https://lixin.blog/assets/img/php_banner.png"
---

Eloquent 让 Model 和数据库表建立对应联系，并且封装好统一的方法，方便开发者调用操作数据。

比如获取名字叫 Tom 的人的年龄：

```php
$person = Person::where('name', 'Tom')->first();
echo $person->age;
```

更多更详细的用法这里不讨论， 记录一下学习 Eloquent ORM 设计  的感想。

 采用原生 php 获取数据库数据一般是由一个数组接收，数组里的 key 对应着数据库列名。在 Eloquent 中，Model 获取的数据直接作为 Model 对象的属性，就像上面例子中使用箭头来获取 age，这一部分有点类似于 JavaScript 中获取对象属性的方法。
