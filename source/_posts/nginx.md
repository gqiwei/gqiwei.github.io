---
title: nginx入门
categories: 笔记
tags:
  - nginx
  - 入门
date: 2022-02-18 11:54:14
description:
---

# ubuntu安装nginx
### 系统配置
Ubuntu 20.04.3 LTS（win10商店下载的Ubuntu）
## nginx包安装
### 安装依赖包
``` linux
apt-get install gcc
apt-get install libpcre3 libpcre3-dev
apt-get install zlib1g zlib1g-dev
apt-get install openssl 
apt-get install libssl-dev
```
### 安装nginx
``` linux
cd /usr/local
mkdir nginx
cd nginx
wget http://nginx.org/download/nginx-1.20.2.tar.gz
tar -xvf nginx-1.20.2.tar.gz
```
### 编译nginx
``` linux
cd nginx-1.20.2
./configure
make
make install
```
### 启动nginx
``` linux
cd /usr/local/nginx/sbin
./nginx
```
### nginx相关命令
```
./nginx #启动
./nginx -s stop #停止
./nginx -s reload #重启
```

# Centos安装nginx
暂无

# nginx负载均衡配置
配置文件在/usr/local/nginx/conf/nginx.conf
``` linux
upstream wserve {
    #ip_hash 可选配置 根据ip进行负载，同一IP访问会固定请求同个服务器地址。 无此配置，访问会在下面配置的ip中轮回请求。
    ip_hash; 
    #server 负载服务ip
    #weight 可选配置，代表权重
    server 127.0.0.1:8080 weight=5; 
    server 127.0.0.1:8081 weight=2;
}
upstream aserve {
    server 210.209.125.142;
}
server {
    listen       80;
    server_name  localhost;

    #访问xxxx会转发到aserve上
    location /xxxx {
        proxy_pass http://aserve;
    }

    #其他路径则会转发到wserve上
    location / {
        proxy_pass http://wserve;
        index  index.html index.htm index.jsp;
    }
}

```