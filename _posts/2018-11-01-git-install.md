---
layout: post
title: 'Git:CentOS安装git-2.18以上版本'
date: 2018-11-01
categories: Git
tags: Git
---

下载编译工具 

yum -y groupinstall "Development Tools"

下载依赖包 

yum -y install zlib-devel perl-ExtUtils-MakeMaker asciidoc xmlto openssl-devel

下载 Git 最新版本的源代码 

>wget https://www.kernel.org/pub/software/scm/git/git-2.18.0.tar.gz  

解压 

tar -zxvf git-2.18.0.tar.gz

进入目录配置 

>cd git-2.18.3 
>./configure --prefix=/usr/git

安装 

>make && make install

配置全局路径 

方法一（暂时生效）

>export PATH="/usr/local/git/bin:$PATH" 
>source /etc/profile

方法二（只对当前登陆用户生效，永久生效）

执行  

>vim ~/.bash_profile

修改文件中 PATH 一行，将

/usr/git/bin 

加入到 

PATH=\$PATH:\$HOME/bin

之后（注意以冒号分隔），保存文件并退出，

执行

>source ~/.bash_profile

使其生效，这种方法只对当前登陆用户生效。

查看git版本 

>git --version

