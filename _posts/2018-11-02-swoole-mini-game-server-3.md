---
layout: post
title: 'Swoole:从头做一个微信小游戏服务器（三）'
date: 2018-11-02
categories: Swoole
tags: Swoole
author: 李昕
cover: 'https://lixin.blog/assets/img/default_banner.png'
---

**输出一个“hello Swoole”**

按照官方的例子，创建一个web服务器

创建文件 server .php

```php
$http = new swoole_http_server("0.0.0.0", 80);
$http->on('request', function ($request, $response) {
    var_dump($request->get, $request->post);
    $response->header("Content-Type", "text/html; charset=utf-8");
    $response->end("<h1>Hello Swoole. #".rand(1000, 9999)."</h1>");
});
$http->start();
```

运行 server .php

>php server.php

浏览器访问地址 http://localhost

服务器会log出

![](https://lixin.blog/assets/post_img/swoole_mini_game_img_2.png)


用apche的ab跑一个压力测试试试

分别测试一下swoole和nodejs+express

10000次请求 100并发

处理器   Intel(R) Xeon(R) CPU E5-2682 v4 @ 2.50GHz 1核
内存     1G

swoole代码

```php
$http = new swoole_http_server("0.0.0.0", 80);
$http->on('request', function ($request, $response) {
    $response->end("Hello World");
});
$http->start();
```

结果(Requests per second )：

Requests per second:    360.99 [#/sec] (mean)

nodejs代码

```js
var express = require('express');
var app = express();
app.get('/', function (req, res) {
  res.send('Hello World!');
});
var server = app.listen(80);
```

结果(Requests per second )：

Requests per second:    439.14 [#/sec] (mean)

可能是我测试的姿势不对。。。

**说回游戏**

游戏模式很简单，开局为两个玩家各分配一个颜色，

玩家在一个8*8的方格里面，在规定的时间内点击方格，方格变成自己对应色，

也可以抢占对手颜色的方格，转换成自己颜色，在计时结束后统计两个颜色的方格数量，多者胜利。

对战性质的游戏要求客户端能实时更新其他玩家的状态，在我们的游戏里就是要求能实时收到对方点击方格的操作，并且绘制游戏画面。所以我们选择搭建一个websocket服务器来做数据处理。

我们的小游戏本质上来就是个一对一聊天室。服务器要做的是维护多个聊天室，平且接收转发数据。

由于swoole进程可以常驻内存，我们可以实现一些在php中比较难搞的想法，比如维护一个常驻进程的变量。

根据swoole文档中对于四种生命周期对象的描述，我们可以创建并维护一个程序全局期的对象来管理游戏房间。

**准备工作**

由于微信的限制，服务器必须配置证书。swoole如果想使用证书，需要在编译时候增加参数 --enable-openssl。






