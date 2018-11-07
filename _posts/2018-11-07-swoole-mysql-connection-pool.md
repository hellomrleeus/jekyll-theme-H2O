---
layout: post
title: 'Swoole:用Swoole实现数据库连接池'
date: 2018-11-07
categories: Swoole
tags: Swoole
author: 李昕
---

数据库建立连接和销毁连接有很大的性能开销，构建连接池可以维护一定连接数量不销毁，降低了数据库创建连接的次数，可以优化并发性能。

php是没有进程池跟连接池这种概念的，所以从单纯php角度设计连接池是很麻烦的，不过我们可以通过swoole，用很简答的代码来实现一个数据库连接池。

swoole安装可以参考官方文档https://wiki.swoole.com/wiki/page/6.html

```php
$serv = new swoole_server("127.0.0.1", 9508);
$serv->set(array(
    'worker_num' => 100,
    'task_worker_num' => 10, //MySQL连接的数量
));

function my_onReceive($serv, $fd, $from_id, $data)
{
    //taskwait就是投递一条任务，这里直接传递SQL语句了
    //然后阻塞等待SQL完成
    $result = $serv->taskwait("show tables");
    if ($result !== false) {
        list($status, $db_res) = explode(':', $result, 2);
        if ($status == 'OK') {
            //数据库操作成功了，执行业务逻辑代码，这里就自动释放掉MySQL连接的占用
            $serv->send($fd, var_export(unserialize($db_res), true) . "\n");
        } else {
            $serv->send($fd, $db_res);
        }
        return;
    } else {
        $serv->send($fd, "Error. Task timeout\n");
    }
}


function my_onTask($serv, $task_id, $from_id, $sql)
{
    static $link = null;
    //static 修饰的函数作用域变量会改变生命周期，但是作用域仍然是局部的
    //mysqli_ping 是为了检查数据库连接状态,mysql连接默认8小时
    if ($link == null || mysqli_ping($link) == false) {
        $link = mysqli_connect("127.0.0.1", "root", "root", "test");
        if (!$link) {
            $link = null;
            $serv->finish("ER:" . mysqli_error($link));
            return;
        }
    }
    $result = $link->query($sql);
    if (!$result) {
        $serv->finish("ER:" . mysqli_error($link));
        return;
    }
    $data = $result->fetch_all(MYSQLI_ASSOC);
    $serv->finish("OK:" . serialize($data));
}

function my_onFinish($serv, $data)
{
    echo "AsyncTask Finish:Connect.PID=" . posix_getpid() . PHP_EOL;
}

$serv->on('Receive', 'my_onReceive');
$serv->on('Task', 'my_onTask');
$serv->on('Finish', 'my_onFinish');
$serv->start();

```

swoole做的实际是创建一个tcp服务器来接收数据库操作请求，收到请求后swoole会投递数据库任务去task进程池，由task来执行返回结果。

由于swoole服务器是一直运行的，所以数据库连接不会中断。