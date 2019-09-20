---
title: 解决win10错误：windows无法启动mongobd
date: 2019-09-17 19:51:28
tags:
- mongoDB
- 故障报错
categories:
- mongoDB
---
安装mongoDB 4.0以后想设置系统服务，发现进入services.msc以后，想尝试启动，出现以下报错
<!--more-->
![微信截图_20190917194807.png](https://i.loli.net/2019/09/17/pkw6cjATflYiGCo.png)

去安装路径/bin目录下找到mongod.cfg这个文件，他是mongo服务启动加载的配置文件，打开，删除最下面`mp:`这一行，保存，启动，成功
![微信截图_20190917195253.png](https://i.loli.net/2019/09/17/SAGqcMnixglTCY2.png)