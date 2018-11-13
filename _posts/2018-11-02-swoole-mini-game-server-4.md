---
layout: post
title: 'Swoole:从头做一个微信小游戏服务器（四）'
date: 2018-11-02
categories: Swoole
tags: Swoole
author: 李昕
cover: 'https://lixin.blog/assets/img/default_banner.png'
---

**第四章 开始设计**

**设计房间队列**

房间队列类应该有一下的功能：

初始化一个数组来保存游戏房间。

维护游戏房间数组，可以增删查。

```php
class RoomArray {
    /**
     * 构造函数，初始化房间数组
     */
    public function __construct() {
        $this->arrRooms = array();
    }
    /**
     * @param string  房间ID
     * @return Room   房间对象
     * 根据房间ID获取房间实例
     */
    public function getRoom($sRoomId) {
        if (isset($this->arrRooms[$sRoomId])) {
            return $this->arrRooms[$sRoomId];
        }
        return false;
    }
    /**
     * @param string  房间ID
     * @param Room    房间对象
     * 存放房间实例到房间数组
     */
    public function setRoom(string $sRoomId, Room $arrRoom) {
        $this->arrRooms[$sRoomId] = $arrRoom;
    }
    /**
     * @return array
     * 获取所有房间
     */
    public function getRooms() {
        return $this->arrRooms;
    }
     /**
     * @param string   房间ID
     * @return int     房间数量   
     * 删除房间实例
     */
    public function delRoom($sRoomId) {
        unset($this->arrRooms[$sRoomId]);
        return count($this->arrRooms);
    }
}
```

**设计房间**

房间应该有一下的功能：

游戏玩家信息增删查;

维护房间状态(等待或者游戏中);

由于一个房间只有两名玩家，所以为两个玩家直接分配角色更方便我们管理。创建房间的玩家分配角色叫host，加入房间的玩家角色叫player。

可以用host的用户ID来充当房间ID

```php
class Room {
    /**
     * @param array $arrHost    host用户信息
     */
    public function __construct($arrHost) {
        $this->sRoomId = $arrHost['userid'];
        $this->arrHost = $arrHost;
        $this->arrHost['room'] = $arrHost['userid'];
        $this->arrHost['ready'] = false;
        $this->playing = false;
    }
    /**
     * @return string Room ID
     * 获取房间实例的RoomID
     */
    public function getRoomId() {
        return $this->sRoomId;
    }
    /**
     * @param array arrHost    host信息
     * 更新房间host信息
     */
    public function setHost($arrHost) {
        $this->arrHost = $arrHost;
        $this->arrHost['room'] = $this->sRoomId;
        $this->arrHost['ready'] = false;
    }
    /**
     * @param array arrPlayer   player信息
     * 更新房间player信息
     */
    public function setPlayer($arrPlayer) {
        $this->arrPlayer = $arrPlayer;
        $this->arrPlayer['room'] = $this->sRoomId;
        $this->arrPlayer['ready'] = false;
    }
    /**
     * @return array 获取房间host信息
     */
    public function getHost() {
        if (isset($this->arrHost)) {
            return $this->arrHost;
        }
        return false;
    }
    /**
     * @return array 获取房间player信息
     */
    public function getPlayer() {
        if (isset($this->arrPlayer)) {
            return $this->arrPlayer;
        }
        return false;
    }
    /**
     * 删除host
     */
    public function delHost() {
        unset($this->arrHost);
    }
    /**
     * 删除player
     */
    public function delPlayer() {
        unset($this->arrPlayer);
    }
    /**
     * @param string sRole 角色
     * @return boolean 两个角色的准备状态
     * 设置角色的准备状态，并且返回两个角色状态取与
     */
    public function setReady(string $sRole) {
        if ($sRole == 'host') {
            $this->arrHost['ready'] = true;
        } elseif ($sRole == 'player') {
            $this->arrPlayer['ready'] = true;
        }
        return $this->arrHost['ready'] && $this->arrPlayer['ready'];
    }
    /**
     * 设置房间状态 playing
     */
    public function playing(bool $bPlaying) {
        $this->playing = $bPlaying;
    }
}
```

这样我们的两个基础类就构造好了。

**服务器设计**

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

客户端发送的消息会在onMessage回调里面获取到。

参数 $frame 是swoole_websocket_frame对象，包含了客户端发来的数据帧信息。

frame−>fd，客户端的socketid，用server->push推送数据时需要用到。

$frame->data，数据内容，可以是文本内容也可以是二进制数据，可以通过opcode的值来判断。

$frame->opcode，WebSocket的OpCode类型，可以参考WebSocket协议标准文档。

$frame->finish， 表示数据帧是否完整，一个WebSocket请求可能会分成多个数据帧进行发送。

onMessage 回调必须被设置，未设置服务器将无法启动。

我们在onMessage里面处理客户端事件，基本的客户端事件应该有:

- 创造\加入房间

- 玩家准备

- 游戏操作

- 游戏结束

服务端端发送给客户端消息是通过 $server->push($fd, $message)方法，其中第一个参数是客户端的socketid，第二个参数是要发送的信息

```php
//初始化房间数组
$oRooms = new RoomArray;

$server->on('message', function (swoole_websocket_server $server, $frame) {
     global $oRooms;
    //解析客户端信息
    $arrClientData = json_decode($frame->data, true);
    
    if ($arrClientData['msg'] == 'join_room') {
    //玩家加入房间
        $oRoom = joinRoom($arrClientData, $frame);
        if (!$oRoom) {
            //房间满了
            $server->push($frame->fd, json_encode(array('msg' => 'no_room')));
        }
        $arrHost = $oRoom->getHost();
        $arrPlayer = $oRoom->getPlayer();
        if ($arrHost && $arrPlayer) {
            //把player信息推送给host
            $server->push($arrHost['fd'], json_encode(array('msg' => 'join_room', 'role' => 'host', 'hostInfo' => $arrHost, 'playerInfo' => $arrPlayer)));
            //把host信息推送给player
            $server->push($arrPlayer['fd'], json_encode(array('msg' => 'join_room', 'role' => 'player', 'hostInfo' => $arrHost, 'playerInfo' => $arrPlayer)));
        }

   }elseif ($arrClientData['msg'] == 'set_ready') {
    //玩家准备
        $oRoom = $oRooms->getRoom($arrClientData['room']);
        if ( $oRoom->setReady($arrClientData['role'])) {
            $oRoom->playing(true);
            //提醒玩家游戏开始
            $server->push($arrHost['fd'], json_encode(array('msg' => 'game_start', 'player' => $arrHost)));
            $server->push($arrPlayer['fd'], json_encode(array('msg' => 'game_start', 'player' => $arrPlayer)));
        }

   }elseif ($arrClientData['msg'] == 'playing') {
    //玩家游戏操作
        $oRoom = $oRooms->getRoom($arrClientData['room']);
        //把host玩家操作发给player
        if ($arrClientData['role'] == 'host') {
            $arrPlayer = $oRoom->getPlayer();
            $server->push($arrPlayer['fd'], json_encode($arrClientData));
        }
        //把player玩家操作发给host
        if ($arrClientData['role'] == 'player') {
            $arrHost = $oRoom->getHost();
            $server->push($arrHost['fd'], json_encode($arrClientData));
        }
   } elseif ($arrClientData['msg'] == 'game_over') {
       //游戏结束删除房间
        $oRooms->delRoom($arrClientData['room']);
    }
});

//加入房间
function joinRoom($arrClientData, $frame) {
    global $oRooms;
    $sRoomId = $arrClientData['room'];
    $arrColors = array('Aqua', 'red', 'blue', 'green', 'yellow', 'purple');
    $iRand1 = rand(0, 2);
    $iRand2 = 5 - $iRand1;
    $arrUser = array(
        'player' => $arrClientData['player'],
        'fd' => $frame->fd,
        'avatar' => $arrClientData['avatar'],
    );
    $oRoom = $oRooms->getRoom($sRoomId);

    //如果没有房间信息，创建房间并成为host
    if (!$oRoom) {
        $arrUser['role'] = 'host';
        $arrUser['room'] = $sRoomId;
        $arrUser['color'] = $arrColors[$iRand1];
        $oRoom = new Room($arrUser);
        $oRooms->setRoom($oRoom->sRoomId, $oRoom);
        echo $arrClientData['player'] . "作为 HOST 创建了房间->" . $oRoom->sRoomId . "\n";
        return $oRoom;
    }
    //如果正在游戏 则返回
    if ($oRoom->playing) {
        return false;
    }
    $arrHost = $oRoom->getHost();
    $arrPlayer = $oRoom->getPlayer();

    //如果不存在host，则当前玩家成为host。如果已经有了host，并且和当前玩家是同一个，则更新信息。
    if (!$arrHost || ($arrHost && $arrHost['player'] == $arrClientData['player'])) {
        $arrUser['role'] = 'host';
        $arrUser['color'] = $arrColors[$iRand1];
        $oRoom->setHost($arrUser);
        echo $arrClientData['player'] . "作为 HOST 加入了房间->" . $sRoomId . "\n";
        return $oRoom;
    }
    //如果不存在player，则当前玩家成为player。如果已经有了player，并且和当前玩家是同一个，则更新信息。
    if (!$arrPlayer || ($arrPlayer && $arrPlayer['player'] == $arrClientData['player'])) {
        $arrUser['role'] = 'player';
        $arrUser['color'] = $arrColors[$iRand2];
        $oRoom->setPlayer($arrUser);
        echo $arrClientData['player'] . "作为 PLAYER 加入了房间->" . $sRoomId . "\n";
        return $oRoom;
    }

    // 房间满了
    return false;

}
```
这样一个最简单的双人游戏服务器就造好了

