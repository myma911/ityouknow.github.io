---
layout: post
title:  CentOS 7开启BBR
no-post-nav: false
category: it
tags: [it]
copyright: it
excerpt: 
---



CentOS 7开启BBR

BBR是谷歌开发的TCP拥堵控制技术，目的是尽量跑满带宽，尽少出现排队的现象。响马老师今天发博文说其境外的某个站点已经支持BBR，于是顺道也在自己的服务器上折腾一下，使其也支持BBR。以下是配置过程：

升级内核
BBR算法已经集成在4.9+的Linux内核中（4.9内核发布于2016-12-13），目前最新版的内核是4.15。为了使用BBR，要先升级系统内核：
```
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
yum --enablerepo=elrepo-kernel install kernel-ml -y
```
更新grub系统引导
首先查看可引导启动的内核：
```
egrep ^menuentry /etc/grub2.cfg | cut -f 2 -d \'
```
找到4.x的内核编号（从0开始），设置为默认内核并重启：
```
grub2-set-default 0 // 0 为新版内核的编号
```
删除旧内核（可选）：
```
yum remove kernel-3.*
```
设置
设置自动加载bbr模块：
```
echo "tcp_bbr" >> /etc/modules-load.d/modules.conf
```
并配置启用bbr： 在 /etc/sysctl.conf文件末尾添加两行：
```
net.core.default_qdisc=fq
net.ipv4.tcp_congestion_control=bbr
```
重启并验证
使用reboot命令重启计算，然后用lsmod | grep bbr命令查看是否有输出，有输出则说明bbr模块已经正常启用。

经过以上步骤，可让服务器支持BBR控制算法，理论上可以有效缓解拥塞，充分利用带宽。