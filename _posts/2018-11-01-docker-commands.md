---
layout: post
title: 'Docker:常用命令'
date: 2018-11-01
categories: Docker
tags: Docker
author: 李昕
cover: 'https://lixin.blog/assets/img/default_banner.jpg'
---

>systemctl start/stop/restart docker
启动/停止/重启 docker

>docker pull [name]

获取镜像

>docker build -t [tagName] [dir]

创建镜像

>docker images 

显示>Docker镜像列表

>docker inspect [镜像ID]

获取镜像的详细信息

>docker search [关键词]

查找关键词镜像列表

>docker ps -a 

查看>Docker后台进程

>docker rmi [镜像标签/镜像ID]

删除镜像

>docker cp [file] [容器ID]:/etc/

复制文件到容器指定位置

>docker rm [容器ID]

删除容器
>docker commit -m "description..." -a "author" [容器ID] [New id]

基于已有容器创建新的镜像

>docker run -it ubuntu /bin/bash

使用镜像创建一个容器，并在其中运行bash应用（-t 分配一个伪终端，-i 让容器标准输入保持打开）

>docker create -it [镜像]

新建一个容器

>docker start [容器ID]

运行处于终止状态的容器

>docker run -d ubuntu [命令]

后台运行容器

>docker logs [容器ID]

查看容器输出信息

>docker stop [-t|--time[=10]] [容器ID]

终止容器，默认等待10s

>docker kill [容器ID]

直接强制终止容器

>docker ps -a -q

查看处于终止状态的容器

>docker restart [容器ID]

重启一个容器

>docker exec -ti [容器ID] /bin/bash

进入到创建容器中运行交互命令 / exit 退出

>docker save -o name.tar ubuntu:14.04

存出镜像到本地为name.tar

>docker load --input name.tar

导入镜像存储文件到本地镜像库

>docker export -o name.tar  [容器ID]

导出停止或运行中的容器到文件中去

>docker import  [OPTIONS] file|URL|- [REPOSITORY[:TAG]]

>runoob@runoob:~$ docker import  my_ubuntu_v3.tar runoob/ubuntu:v4  
>sha256:63ce4a6d6bc3fabb95dbd6c561404a309b7bdfc4e21c1d59fe9fe4299cbfea39
>runoob@runoob:~$ docker images runoob/ubuntu:v4
>REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
>runoob/ubuntu       v4                  63ce4a6d6bc3        20 seconds ago      142.1 MB

导入容器导出的文件成为镜像
 
Tips

docker里面127.0.0.1 指向容器本地ip

>/sbin/ip route|awk '/default/ { print $3 }' 

容器里查询主机ip

>--link=myRedis:redis_host  

Docker会在容器中的/etc/hosts路径下为“redis”创建一个入口，并指向“redis_host ”容器的IP地址
 
>docker logs -f

 容器名字 实时查看日志