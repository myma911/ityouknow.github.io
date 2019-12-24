---
layout: post
title:  V2Ray教程
no-post-nav: false
category: it
tags: [it]
copyright: it
excerpt: 
---


拯救被墙的服务器
作者 tlanyan | 2019年10月2日37 条评论
人在江湖飘，哪能不挨刀。经常科学上网的网友，自然对vps被屏蔽、无法建立连接等现象再熟悉不过。遇到这种情况，新手该怎么处理？经验丰富的老司机又如何做到保持外网不断？

本文先分析服务器被block的现象，再根据多年经验给出解决办法以及降低被墙概率的建议。

被墙分析
遇到无法顺利科学上网的情形，首先应该判断自身网络是否有问题。操作页很简单：浏览器打开百度，能顺畅打开说明网络没问题；打不开或者很难打开，说明自身网络不行，要先解决。

接下来查看服务器vps是否正常运行。ssh或远程桌面连接到vps，查看服务端程序有没有正常运行。如果程序意外退出，重启后再试试能否科学上网。如果不能连接到服务器，登录服务器运营商管理后台，查看是否有欠费、流量用尽、被攻击等情形。服务器处于异常状态，则应先自行或联系客服解决，之后再重试能否科学上网。

做完上述两步，有如下现象可判定服务器已经处于被墙状态：

管理后台显示vps运行正常，但国内无法ping通，境外ip可以（ip被列入黑名单）；
能ping通，也能telnet过去，但ssh等软件无法正常使用，抓包或日志显示应答超时（tcp阻断）。 
除ip被墙，还有以下姿势：

域名被墙：国内域名被解析到错误的ip导致网站无法打开（dns污染）；
端口被墙：22/80/443等常用端口一切正常，科学上网端口无法连接（本次国庆前后现象最明显）；
tcp干扰：连接不稳定，经常断线，例如ssh连到境外服务器经常莫名其妙被断线。
比较简单有效的方法是多测试。某些网络（例如电信宽带）或设备（例如电脑）能正常科学上网，但是其它不行，也许只是配置或者运营商线路问题；如果只有境外朋友的能正常连上，就说明真的被墙了。

补救措施
无论哪种被墙方式，都给出同一个信号：老大哥盯上你了！

除了今后更谨慎，可以采取如下补救措施抢救一下：

国内服务器转发。阿里云等厂商的国际线路优先级比较高，其服务器与被墙vps能直接通信，这种情形下做个转发即可正常使用，代价是多买一个国内服务器。顺道说一下，如果国外线路不好，用转发方式也能大幅提升网速，以及降低被墙概率；教程：通过国内服务器转发流量
换ip。vultr等厂商提供随意更换ip的服务，可试试更换ip或者添加新的ip；
上cdn。（国内）cdn的公共ip基本不会被封，将域名解析到cdn，流量经过cdn再到被墙服务器，代价是需要一个域名并配置cdn；
换端口。针对端口被墙情形，更换新端口，采用另一种加密方式。
实在不行，那只能重建（重买）一台新服务器。
以上措施应该能让你多苟延残喘一会，如果还是很快惨遭封杀，强烈建议直接上带伪装的V2Ray保平安。配置教程：V2Ray教程 以及V2Ray高级技巧：流量伪装。

实用建议
最近敏感时期，本人虽没有挂过vps，但ss端口被封了好几个，亦感觉到事态严重。鉴于节后环境不一定更宽松，之后还是谨慎为妙。目前本人放心可靠的科学上网软硬件配置为：

常用端口的ss一台，国内服务器中转，除国内服务器外其他ip不可访问。教程：通过国内服务器转发流量
websocket+tls+v2ray服务器一台，搭配一个日ip上百的https小站。
这两组配置让本人撑过了一段段风雨飘摇的日子，也是目前情形下本人力荐的配置。

除上述软硬件配置，以下一些实用事项也请多加注意：

科学上网时，尽量不要同时用国产的杀毒软件、浏览器、输入法等；
尽量用pac模式科学上网，少用要全局模式；
自用为佳，少分享，机场很容易挂，流量太大也容易被重点照顾；
访问外网时多看少说，尤其是安全性不高的小网站，被脱裤后代理ip信息便一览无遗，找到代理后的你分分钟的事；
国内服务器中转时记得屏蔽其他ip；
搭建服务器时同时搭建一个网站，出问题时能迅速确定ip被封还是端口问题。同时因为网站的存在，能一定程度上迷惑墙，不会轻易封杀；
重要的事情重复三遍：伪装的V2Ray大法好！伪装的V2Ray大法好！伪装的V2Ray大法好！
目前环境建议低调、少上外网，非用不可时建议上v2ray，两篇配置教程：V2Ray教程 以及V2Ray高级技巧：流量伪装。