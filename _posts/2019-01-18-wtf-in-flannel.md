---
layout: post
title: "阿里云ECS部署Flannel遇到的坑"
date: 2019-01-18
categories: Flannel
tags: Flannel
author: 李昕
---

业务扩展，集群搞起来，记录一下 Flannel 部署时候遇到的坑，这些问题可能其他人不会遇到，因为要部署 Flannel 的三台服务器内网不通。

三台服务器都是阿里云 ECS，CentOS7，区域分别是北京、上海和深圳，并且分别属于三个阿里云账号。为什么这么买？别问，问就是因为有新用户优惠。。。

遇到第一个问题就是 Flannel 配置参数。

我是通过 yum 来安装 Flannel，可以通过修改/etc/systemd/system/flanneld.service 文件来更改 Flannel 启动参数。一般网上给的例子配置参数里面是 iface

> --iface=xxxxxx

官网这部分其实介绍了两个参数。

> -iface string: Interface to use (IP or name) for inter-host communication.
>
> -public-ip string: IP accessible by other nodes for inter-host communication.

iface 参数可以是内网地址，也可以是网卡名字， 但不可以是公网 ip。public-ip 就可以使用公网 ip。

上面两个参数根据情况使用一个就可以。

遇到第二个问题就是 Flannel 修改了 interface 之后不生效。

刚开始配置时候我是基于内网配置，后来才知道内网不通，于是又全部改成基于外网配置。修改了 Flannel 接口地址，从内网 ip 改为公网 ip（iface 改为 public-ip）并且重新启动，然后再重启 docker。

尝试在 A 服务器 docker 容器中 ping 另一台 B 服务器容器地址，还是 ping 不通，然后就开始逐一排查。

首先使用 tcpdump 监听 Flannel 虚拟网卡，发现可以抓到数据包，说明从容器中发送数据包已经经过 docker 网卡跳到 Flannel 了。

再监听 eth0 网卡，同样可以抓到数据包，但是重点来了，docker 容器中 ping 目的地址仍然是 Flannel 修改前的地址，即 B 服务器的内网地址。

查看 etcd 路径下 ip 地址分片，新的地址（公网地址）确实已经保存去了。

尝试重新启动 A 服务器 Flannel 和 docker，再一次从容器中 ping 另外 B 服务器中的容器 IP 地址，目的地址仍然没有改成新的。怀疑是 Flannel 内部维护的地址列表不支持动态更新？

最后注意的是阿里云 ECS 安全策略管理，响应的端口要开放，比如 VxLAN 端口是 8427。
