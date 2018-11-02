---
layout: post
title: 'Docker:安装Redis'
date: 2018-11-01
categories: Docker
tags: Docker
author: 李昕
---

**运行容器**

>runoob@runoob:~/redis$ docker run -p 6379:6379 -v $PWD/data:/data  -d redis:3.2 redis-server --appendonly yes
>43f7a65ec7f8bd64eb1c5d82bc4fb60e5eb31915979c4e7821759aac3b62f330
>runoob@runoob:~/redis$

**命令说明**

-p 6379:6379 : 将容器的6379端口映射到主机的6379端口

-v $PWD/data:/data : 将主机中当前目录下的data挂载到容器的/data

redis-server --appendonly yes : 在容器执行redis-server启动命令，并打开redis持久化配置

**查看容器启动情况**

>runoob@runoob:~/redis$ docker ps
>CONTAINER ID   IMAGE        COMMAND                 ...   PORTS                      NAMES
>43f7a65ec7f8   redis:3.2    "docker-entrypoint.sh"  ...   0.0.0.0:6379->6379/tcp     agitated_cray

**连接、查看容器**

使用redis镜像执行redis-cli命令连接到刚启动的容器,主机IP为172.17.0.1

>runoob@runoob:~/redis$ docker exec -it 43f7a65ec7f8 redis-cli
>172.17.0.1:6379> info
># Server
>redis_version:3.2.0
>redis_git_sha1:00000000
>redis_git_dirty:0
>redis_build_id:f449541256e7d446
>redis_mode:standalone
>os:Linux 4.2.0-16-generic x86_64
>arch_bits:64
>multiplexing_api:epoll