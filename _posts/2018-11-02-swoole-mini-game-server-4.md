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


