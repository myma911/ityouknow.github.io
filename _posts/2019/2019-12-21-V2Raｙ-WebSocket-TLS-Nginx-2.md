---
layout: post
title:  V2Ray教程
no-post-nav: false
category: it
tags: [it]
copyright: it
excerpt: 
---

https://www.cnblogs.com/bndong/p/11763377.html


v2raｙ 是一个模块化的代理工具，支持 VMess，Socks，HTTP，Shadowsocks 等等协议，并且附带很多高级功能，HTTP，TLS 等等。

关键词限制，全文 v2raｙ 中的 ｙ 为全角字符，运行相关命令时请自行替换为半角的 y

1|0前提条件
境外 VPS 一台，并已编译安装 Nginx
域名一个，解析至该 VPS
基本的 Linux 技巧
2|0测试环境
Domain Name
www.test.org

CentOS
LSB Version:    :core-4.1-amd64:core-4.1-noarch
Distributor ID: CentOS
Description:    CentOS Linux release 7.3.1611 (Core)
Release:    7.3.1611
Codename:   Core
3|0安装
3|1V2Raｙ
安装 V2Raｙ
以下指令假设已在 su 环境下，如果不是，请先运行 sudo su

bash <(curl -L -s https://install.direct/go.sh)
此脚本会自动安装以下文件：

/usr/bin/v2raｙ/v2raｙ：V2Raｙ 程序；
/usr/bin/v2raｙ/v2ctl：V2Raｙ 工具；
/etc/v2raｙ/config.json：配置文件；
/usr/bin/v2raｙ/geoip.dat：IP 数据文件
/usr/bin/v2raｙ/geosite.dat：域名数据文件 此脚本会配置自动运行脚本。自动运行脚本会在系统重启之后，自动运行 V2Raｙ。目前自动运行脚本只支持带有 Systemd 的系统，以及 Debian / Ubuntu 全系列。
设置开机启动
systemctl enable v2raｙ
3|2SSL
安装 EPEL
yum -y install epel-release
EPEL 的全称叫 Extra Packages for Enterprise Linux 。EPEL是由 Fedora 社区打造，为 RHEL 及衍生发行版如 CentOS、Scientific Linux 等提供高质量软件包的项目。装上了 EPEL 之后，就相当于添加了一个第三方源。

安装 certbot
yum -y install certbot
certbot 是一款让你的网站自动部署 Let's Encrypt 颁发的免费数字证书

申请 SSL 证书
certbot certonly --standalone -d www.test.org
如果申请成功，证书和私钥路径如下：

/etc/letsencrypt/live/www.test.org/fullchain.pem
/etc/letsencrypt/live/www.test.org/privkey.pem
4|0配置
4|1Nginx
打开域名的配置文件

server {
  listen 80;
  listen 443 ssl http2;
  ssl_certificate /etc/letsencrypt/live/www.test.org/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/www.test.org/privkey.pem;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
  ssl_ciphers TLS13-AES-256-GCM-SHA384:TLS13-CHACHA20-POLY1305-SHA256:TLS13-AES-128-GCM-SHA256:TLS13-AES-128-CCM-8-SHA256:TLS13-AES-128-CCM-SHA256:EECDH+CHACHA20:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;
  ssl_prefer_server_ciphers on;
  ssl_session_timeout 10m;
  ssl_session_cache builtin:1000 shared:SSL:10m;
  ssl_buffer_size 1400;
  add_header Strict-Transport-Security max-age=15768000;
  ssl_stapling on;
  ssl_stapling_verify on;
  server_name www.test.org;
  access_log /data/wwwlogs/www.test.org_nginx.log combined;
  index index.html index.htm index.php;
  root /data/wwwroot/www.test.org;
  if ($ssl_protocol = "") { return 301 https://$host$request_uri; }

  include /usr/local/nginx/conf/rewrite/other.conf;
  #error_page 404 /404.html;
  #error_page 502 /502.html;

  location /ray {
    proxy_pass       http://127.0.0.1:23333;
    proxy_redirect             off;
    proxy_http_version         1.1;
    proxy_set_header Upgrade   $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_set_header Host      $http_host;
  }
}
4|2V2Raｙ
备份配置文件
cp /etc/v2raｙ/config.json /etc/v2raｙ/config.json_bak
编辑配置文件
vi /etc/v2raｙ/config.json
{
  "inbounds": [
    {
      "port": 23333,
      "listen":"127.0.0.1",
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx",
            "alterId": 4
          }
        ]
      },
      "streamSettings": {
        "network": "ws",
        "wsSettings": {
        "path": "/ray"
        }
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    }
  ]
}
id 为 UUID 可以用这个网站生成：https://www.uuidgenerator.net/
服务端配置来说，主要关心 inbound 中配置，包括端口，协议，和 id 以及 alterId。这些配置需要和客户端一致。
当需要多人使用时，clients 中可以配置多个。

v2raｙ 支持以下协议，默认的协议为 VMess

Blackhole
Dokodemo-door
Freedom
HTTP
Shadowsocks
Socks
VMess
5|0Linux 安全设置
5|1防火墙 firewalld
关闭防火墙或者开放端口

关闭防火墙
systemctl stop firewalld.service
开放端口
添加

firewall-cmd --zone=public --add-port=443/tcp --permanent
--permanent 永久生效，没有此参数重启后失效。

重新载入

firewall-cmd --reload
5|2SELinux
关闭 SELinux

vi /etc/selinux/config
SELINUX=disabled
setenforce 0
SELinux 是一种基于 域-类型 模型（domain-type）的强制访问控制（MAC）安全系统，它由NSA编写并设计成内核模块包含到内核中，相应的某些安全相关的应用也被打了SELinux的补丁，最后还有一个相应的安全策略。任何程序对其资源享有完全的控制权。假设某个程序打算把含有潜在重要信息的文件扔到/tmp目录下，那么在DAC情况下没人能阻止他。SELinux提供了比传统的UNIX权限更好的访问控制。

6|0启动
systemctl start v2raｙ
systemctl start nginx
查看状态
systemctl status v2raｙ
systemctl status nginx
7|0客户端
Windows客户端：
V2RaｙW
V2RaｙN
V2RaｙS
Clash
Android客户端：
V2RaｙNG
BifrostV
MacOS客户端
V2RaｙX
V2RaｙU
ClashX
iOS(iPhone/iPad)客户端：
Shadowrocket
i2Ray
Kitsunebi
pepi
Quantumult
国区 App Store 搜索不到，可使用美区账户，用户名 tangyuan9102@gmail.com ，密码 QWER1234poiu 登录app store再尝试。

关于客户端的配置，这里以 V2RaｙX 举例：



Address: 你的服务器IP/地址，后边的是端口，对应服务器配置文件内 inbound 部分的 port
User ID: 服务器配置文件内 inbound 部分的 ID
alterId: 服务器配置文件内 inbound 部分的 alterId




8|0备注
V2Raｙ 的客户端 Core 和服务端 Core 版本需要一致。
V2Raｙ 要求客户端与服务器的时间差不超过1分钟（与时区无关）。
本教程仅供学习交流，请勿违反国家法律法规，否则后果自负！