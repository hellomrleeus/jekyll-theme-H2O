---
layout: post
title: 'Swoole:从头做一个微信小游戏服务器（四）'
date: 2018-11-02
categories: Swoole
tags: Swoole
author: 李昕
cover: 'https://lixin.blog/assets/img/default_banner.png'
---

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
     * 获取所有房间
     */
    public function getRooms() {
        return $this->arrRooms;
    }
     /**
     * @param string   房间ID
     * @return      房间数量   
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

现在可以




