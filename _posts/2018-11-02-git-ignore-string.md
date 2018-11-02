---
layout: post
title: 'Git:在'
date: 2018-11-01
categories: Git
tags: Git
author: 李昕
cover: 'https://lixin.blog/assets/img/git_banner.png'
---

git在提交和check out时候会分别执行两个程序 “clean”和“smudege”，所以我们可以让这两个执行我们自定的脚本或命令，来实现一些功能，包括替换文件中的字符串。

如果开发开源项目时候想要隐藏一些如域名等敏感信息，或者项目pro环境和自己开发环境的配置有区别，我们就可以通过clean和smudge来替换掉一些信息。

**首先创建一个gitattributes 文件**

your_project/.gitattributes (会被提交到库中)

或者

your_project/.git/info/attributes (不会被提交到库中)

**添加一个自定义过滤配置**

比如要修改config.php文件中的字符串，则需要在文件中添加

>config.php filter=gitignore

其中“gitignore”是自定义的节点名字

**修改项目中.git/config文件**

在config文件中增加节点

>[filter "gitignore"] #引号中的名称要和上面保持一致
>    clean = sed 's/local_string/remote_string/'g   #出仓时候替换 /local/remote/
>    smudge = sed 's/remote_string/local_string/'g  #进仓时候替换 /remote/local/

sed 是linux下的命令，在git bash中也有执行环境，所以除了使用sed替换意外，sed的其他功能或者是其他git bash下有的命令应该都可以执行