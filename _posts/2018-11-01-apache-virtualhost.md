---
layout: post
title: 'Apache: 虚拟域名配置'
date: 2018-11-01
categories: Apache
tags: Apache
author: 李昕
cover: 'https://lixin.blog/assets/img/apache_banner.png'
---

配置虚拟域名

```xml
# Ensure that Apache listens on port 80
Listen 80
<VirtualHost *:80>
    DocumentRoot "/www/example1"
    ServerName www.example.com

    # Other directives here
</VirtualHost>

<VirtualHost *:80>
    DocumentRoot "/www/example2"
    ServerName www.example.org

    # Other directives here
</VirtualHost>
```

详细配置http://httpd.apache.org/docs/current/zh-cn/vhosts/examples.html
