---
layout: post
title: 'Swoole:使用Systemctl管理Swoole服务(转)'
date: 2018-11-02
categories: Swoole
tags: Swoole
author: 月光光
---
本文转自https://www.helloweba.net/php/587.html

在使用nodejs做服务器时候一般会开启守护进程来保证node进程挂掉后可以自动重启。

我们把Swoole主服务程序做成系统服务后，这个服务一般是在后台运行的，如我们之前的邮件服务和聊天服务，我们希望把这些服务添加到系统环境中，可以随机器自启动，可以管理swoole服务的启动、停止和重启服务。

**前言**

如果要启动swoole服务，需要手动执行代码如：php chatServer.php，这样就启动了聊天服务端。那如果我们要停止Swoole服务呢？我们可以使用kill -9 <pid>， pid对应的是swoole服务的主进程。这样操作起来比较麻烦，而且可能导致swoole子进程杀不掉的情况。

我们需要一个可以用来管理swoole服务状态的工具或脚本，幸运的是，在CentOS7上我们可以使用Systemctl来轻松管理Swoole服务。

Systemctl配置说明

CentOS7的服务systemctl脚本存放在:/usr/lib/systemd/，服务又分为系统服务（system）和用户服务（user），需要开机不登陆就能运行的程序，存在系统服务里，即：/usr/lib/systemd/system目录下。

CentOS7的每一个服务以.service结尾，一般会分为3部分：[Unit]、[Service]和[Install]，结构和说明可以参照以下：

>[Unit]部分主要是对这个服务的说明，内容包括Description和After，Description 用于描述服务，After用于描述服务类别
>
>[Service]部分是服务的关键，是服务的一些具体运行参数的设置.
>
>Type=forking 是后台运行的形式，
>User=users 是设置服务运行的用户,
>Group=users 是设置服务运行的用户组,
>PIDFile为存放PID的文件路径，
>ExecStart为服务的具体运行命令,
>ExecReload为重启命令，
>ExecStop为停止命令，
>PrivateTmp=True表示给服务分配独立的临时空间
>
>[Install]部分是服务安装的相关设置，可设置为多用户的

注意：[Service]部分的启动、重启、停止命令全部要求使用绝对路径，使用相对路径则会报错！

**配置Swoole服务**

有了Systemctl，我们可以轻松配置我们的Swoole服务，下面以Swoole聊天服务为例：

首先在/usr/lib/systemd/system/目录下，新建文件swoolechat.service，并加入以下代码：

>[Unit]
Description=Swoole Chat Server
After=network.target syslog.target

>[Service]
Type=forking
LimitNOFILE=65535
ExecStart=/usr/local/php/bin/php /home/web/swoole/public/chatServer.php
ExecReload=/bin/kill -USR1 $MAINPID
Restart=always

>[Install]
WantedBy=multi-user.target graphical.target
然后保存好文件，并使用如下命令重新载入所有的[Unit]文件，确保我们添加进去的service能被系统加载并生效。

>systemctl  daemon-reload

**验证**

现在我们来验证服务，首先启动swoole服务：

>systemctl start swoolechat.service

然后使用以下命令查看swoole服务是否正常:

>systemctl status swoolechat.service

如果看到绿色的active (running)字样，那么恭喜你服务启动成功了。

如果还不放心，可以使用命令netstat -lntp查看swoole服务端口是否正常。

**停止、重启swoole服务：**

>systemctl stop|restart swoolechat.service

现在你就可以设置开机启动swoole服务了：

>systemctl enable swoolechat.service

综上，我们可以使用命令轻松管理Swoole服务了。