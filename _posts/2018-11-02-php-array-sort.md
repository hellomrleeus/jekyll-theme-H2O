---
layout: post
title: 'PHP:数组排序'
date: 2018-11-02
categories: PHP
tags: PHP
author: 李昕
cover: 'https://lixin.blog/assets/img/php_banner.jpg'
---

sort() 函数用于对数组单元从低到高进行排序。

rsort() 函数用于对数组单元从高到低进行排序。

asort() 函数用于对数组单元从低到高进行排序并保持索引关系。

arsort() 函数用于对数组单元从高到低进行排序并保持索引关系。

ksort() 函数用于对数组单元按照键名从低到高进行排序。

krsort() 函数用于对数组单元按照键名从高到低进行排序。

以上函数接收两个参数array和sortingtype(可选);

sorting type:

0 = SORT_REGULAR - 默认。把每一项按常规顺序排列（Standard ASCII，不改变类型）

1 = SORT_NUMERIC - 把每一项作为数字来处理

2 = SORT_STRING - 把每一项作为字符串来处理

3 = SORT_LOCALE_STRING - 把每一项作为字符串来处理，基于当前区域设置（可通过 setlocale() 进行更改）

4 = SORT_NATURAL - 把每一项作为字符串来处理，使用类似 natsort() 的自然排序

5 = SORT_FLAG_CASE - 可以结合（按位或）SORT_STRING 或 SORT_NATURAL 对字符串进行排序，不区分大小写

