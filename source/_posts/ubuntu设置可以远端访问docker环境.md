---
title: ubuntu设置可以远端访问docker环境
date: 2020-01-11 16:32:47
tags:
- linux
- Ubuntu
- docker
categories:
- docker
---
最近使用docker发现无法使用远端访问Linux的docker环境
#### 解决办法
环境：Ubuntu 16.04  
步骤：
* 创建/etc/systemd/system/docker.service.d目录

```
sudo mkdir /etc/systemd/system/docker.service.d
```
* 创建一个/etc/systemd/system/docker.service.d/http-proxy.conf文件

```
sudo vim /etc/systemd/system/docker.service.d/http-proxy.conf

[Service]

ExecStart=

ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock
```

* 刷新配置

```
sudo systemctl daemon-reload
```
* 重启docker守护进程

```
sudo systemctl restart docker
```