---
layout: post
title: 'Docker:安装Nginx反向代理'
date: 2018-11-01
categories: Docker
tags: Docker
author: 李昕
cover: 'https://lixin.blog/assets/img/docker_banner.png'
---

**方法一、通过 Dockerfile构建**

创建Dockerfile

首先，创建目录nginx,用于存放后面的相关东西。

>runoob@runoob:~$ mkdir -p ~/nginx/www ~/nginx/logs ~/nginx/conf

www目录将映射为nginx容器配置的虚拟目录

logs目录将映射为nginx容器的日志目录

conf目录里的配置文件将映射为nginx容器的配置文件

进入创建的nginx目录，创建Dockerfile

```
FROM debian:jessie
MAINTAINER NGINX Docker Maintainers "docker-maint@nginx.com"
ENV NGINX_VERSION 1.10.1-1~jessie
RUN apt-key adv --keyserver hkp://pgp.mit.edu:80 --recv-keys  573BFD6B3D8FBC641079A6ABABF5BD827BD9BF62 \
        && echo "deb http://nginx.org/packages/debian/ jessie nginx" >> /etc/apt/sources.list \
        && apt-get update \
        && apt-get install --no-install-recommends --no-install-suggests -y \
                                                ca-certificates \
                                                nginx=${NGINX_VERSION} \
                                                nginx-module-xslt \
                                                nginx-module-geoip \
                                                nginx-module-image-filter \
                                                nginx-module-perl \
                                                nginx-module-njs \
                                                gettext-base \
        && rm -rf /var/lib/apt/lists/*
#forward request and error logs to docker log collector
RUN ln -sf /dev/stdout /var/log/nginx/access.log \
        && ln -sf /dev/stderr /var/log/nginx/error.log
EXPOSE 80 443
CMD ["nginx", "-g", "daemon off;"]
```

通过Dockerfile创建一个镜像，替换成你自己的名字

>docker build -t nginx

创建完成后，我们可以在本地的镜像列表里查找到刚刚创建的镜像

>runoob@runoob:~/nginx$ docker images nginx
>REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
>nginx               latest              555bbd91e13c        3 days ago          182.8 MB
 
**方法二、docker pull nginx**

查找Docker Hub上的nginx镜像

runoob@runoob:~/nginx$ docker search nginx

>NAME                      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
nginx                     Official build of Nginx.                        3260      [OK]      
jwilder/nginx-proxy       Automated Nginx reverse proxy for docker c...   674                  [OK]
richarvey/nginx-php-fpm   Container running Nginx + PHP-FPM capable ...   207                  [OK]
million12/nginx-php       Nginx + PHP-FPM 5.5, 5.6, 7.0 (NG), CentOS...   67                   [OK]
maxexcloo/nginx-php       Docker framework container with Nginx and ...   57                   [OK]
webdevops/php-nginx       Nginx with PHP-FPM                              39                   [OK]
h3nrik/nginx-ldap         NGINX web server with LDAP/AD, SSL and pro...   27                   [OK]
bitnami/nginx             Bitnami nginx Docker Image                      19                   [OK]
maxexcloo/nginx           Docker framework container with Nginx inst...   7                    [OK]
...

这里我们拉取官方的镜像

>runoob@runoob:~/nginx$ docker pull nginx

等待下载完成后，我们就可以在本地镜像列表里查到REPOSITORY为nginx的镜像。
 
使用nginx镜像

docker 部署个nginx

直接一行命令搞定：

>docker run \  
--name nginx-health-web-pc \  
  -d -p 8080:80 \  
  -v /usr/docker/nginx/html:/usr/share/nginx/html \  
  nginx  


此时nginx加载的配置文件是他的主配置文件nginx.conf，如果我们想用自定义配置，需要把我们的配置文件挂载到容器中nginx.conf目录里面
 
我们先看挂载好的命令：


 启动docker的命令
>docker run \
-p 8080:80 \
--name nginx_web \
-v /Users/roverliang/docker_study/log/:/var/log/nginx \
-v /Users/roverliang/docker_study/etc/nginx.conf:/etc/nginx/nginx.conf \
-v /Users/roverliang/docker_study/html/:/usr/share/nginx/html \
-d \
nginx 

上面三个挂载目录以此对应着log目录、配置文件目录和文档目录

Tips:
 
nginx容器目录

/usr/share/nginx/html

/etc/nginx/nginx.conf

/var/log/nginx/access.log

/var/log/nginx/error.log

letsencrypt签发证书路径

/etc/letsencrypt/live/www.domain.com/  