---
layout: post
title: 'Docker:CentOS安装Docker'
date: 2018-11-01
categories: Docker
tags: Docker
author: 李昕
---

Docker支持以下的CentOS版本：

CentOS 7 (64-bit)

CentOS 6.5 (64-bit) 或更高的版本

 
**前提条件**

目前，CentOS 仅发行版本中的内核支持 Docker。

Docker 运行在 CentOS 7 上，要求系统为64位、系统内核版本为 3.10 以上。

Docker 运行在 CentOS-6.5 或更高的版本的 CentOS 上，要求系统为64位、系统内核版本为 2.6.32-431 或者更高版本。
 
使用 yum 安装（CentOS 7下）

Docker 要求 CentOS 系统的内核版本高于 3.10 ，查看本页面的前提条件来验证你的CentOS 版本是否支持 Docker 。
通过 uname -r 命令查看你当前的内核版本

>[root@runoob ~]# uname -r 
>3.10.0-514.26.2.el7.x86_64

**安装 Docker**

Docker 软件包和依赖包已经包含在默认的 CentOS-Extras 软件源里，安装命令如下：

>[root@runoob ~]# yum -y install docker-io

安装完成。

启动 Docker 后台服务

>[root@runoob ~]# systemctl start docker

测试运行 hello-world

>[root@runoob ~]#docker run hello-world

由于本地没有hello-world这个镜像，所以会下载一个hello-world的镜像，并在容器内运行。
 
**使用脚本安装 Docker**

1、使用 sudo 或 root 权限登录 Centos。

2、确保 yum 包更新到最新。

>$ sudo yum update

3、执行 Docker 安装脚本。

>$ curl -fsSL https://get.docker.com/ | sh

执行这个脚本会添加 docker.repo 源并安装 Docker。

4、启动 Docker 进程。

>$ sudo service docker start

5、验证 docker 是否安装成功并在容器中执行一个测试的镜像。

>$ sudo docker run hello-world
>docker ps
 
**镜像加速**

鉴于国内网络问题，后续拉取 Docker 镜像十分缓慢，可以使用网易的镜像地址：

>http://hub-mirror.c.163.com。

新版的 Docker 使用 /etc/docker/daemon.json（Linux） 

或者

%programdata%\docker\config\daemon.json（Windows） 

来配置 Daemon。

请在该配置文件中加入（没有该文件的话，请先建一个）：

{
  "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
 