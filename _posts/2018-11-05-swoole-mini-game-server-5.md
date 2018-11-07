---
layout: post
title: 'Swoole:从头做一个微信小游戏服务器（五）'
date: 2018-11-05
categories: Swoole
tags: Swoole
author: 李昕
cover: 'https://lixin.blog/assets/img/default_banner.png'
---

**第四章 服务器设计**

我们先看看官方websocket服务器的demo

```php
$server = new swoole_websocket_server("0.0.0.0", 9501);

$server->on('open', function (swoole_websocket_server $server, $request) {
    echo "server: handshake success with fd{$request->fd}\n";
});

$server->on('message', function (swoole_websocket_server $server, $frame) {
    echo "receive from {$frame->fd}:{$frame->data},opcode:{$frame->opcode},fin:{$frame->finish}\n";
    $server->push($frame->fd, "this is server");
});

$server->on('close', function ($ser, $fd) {
    echo "client {$fd} closed\n";
});

$server->start();
```

客户端发送的消息会在onMessage回调里面获取到，

参数 $frame 是swoole_websocket_frame对象，包含了客户端发来的数据帧信息。

frame−>fd，客户端的socketid，用frame−>fd，客户端的socketid，用server->push推送数据时需要用到。

$frame->data，数据内容，可以是文本内容也可以是二进制数据，可以通过opcode的值来判断。

$frame->opcode，WebSocket的OpCode类型，可以参考WebSocket协议标准文档

$frame->finish， 表示数据帧是否完整，一个WebSocket请求可能会分成多个数据帧进行发送

onMessage 回调必须被设置，未设置服务器将无法启动
