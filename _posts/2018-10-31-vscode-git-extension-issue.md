---
layout: post
title: '在使用VSCode中Git扩展时遇到的加密SSH Key问题'
date: 2018-10-31
author: 李昕
tags: VSCode
cover: 'https://lixin.blog/assets/img/default_banner.jpg'
---

环境：
- win10
- Git客户端windows版本
- VSCode 1.282
以前使用git账号密码模式来管理代码库的，搭建博客时候开了新的项目，使用了SSH模式，在fetch的时候遇到了下面的问题

***Git Error: Permission Denied***

详细的log是



>git@gitlab.com: Permission denied (publickey).
>fatal: Could not read from remote repository.
>
>Please make sure you have the correct access rights
>and the repository exists.
>\> git fetch
>\> git show :_posts/about.md
>git@gitlab.com: Permission denied (publickey).
>fatal: Could not read from remote repository.



在命令行里面使用git fetch --all，提示我输入我输入密码，输入密码之后就成功了，所以应该是VSCode的问题。
VSCode会直接使用我的私钥，跳过了输入密码的过程，所以会提醒没有权限


Google一下有两种解决方法。


第一种是在生成SSH Key时候不用密码。


第二种是需要在命令行启动VSCode:


1、打开CMD或者PowerShell

2、执行 "start-ssh-agent" 命令，然后输入密码

3、执行 "code" 命令，启动VSCode


然后就可以了，不过关掉后从其他路径启动VSCode仍然会提醒权限错误，应该是在ssh-agent在启动时候会在系统设置一些环境变量或者上下文。换了环境VSCode就自然就无法使用ssh-agent了。




参考自https://nathan.alner.net/2015/08/24/vs-code-ide-with-passphrased-git-ssh-keys/



