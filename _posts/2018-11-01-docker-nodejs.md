---
layout: post
title: 'Docker:部署Nodejs项目'
date: 2018-11-01
categories: Docker
tags: Docker
author: 李昕
cover: 'https://lixin.blog/assets/img/docker_banner.png'
---

在项目根目录上创建Dockerfile文件

```
FROM node
RUN mkdir -p /home/server
WORKDIR /home/server
COPY . /home/server
RUN npm install
EXPOSE 8888
CMD  npm start
```

执行

>docker build -t tagName .


Tips:

关于 npm 修改 淘宝的源

npm config set registry http://registry.npm.taobao.org/
 
关于守护进程  添加restart=always 也可以 

如果 node 进程挂掉对应的容器也会停止运行

