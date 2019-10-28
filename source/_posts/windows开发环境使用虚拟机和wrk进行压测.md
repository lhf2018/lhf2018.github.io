---
title: windows开发环境使用虚拟机和wrk进行压测
date: 2019-10-24 15:03:33
tags:
- 虚拟机
- wrk
categories:
- 压测
---
使用windows开发应用的时候需要使用wrk压力测试，发现wrk只能用在类unix系统，所以打算使用虚拟机连通物理机进行压测
#### 连通虚拟机和物理机
首先连通虚拟机和物理机，选择虚拟机的编辑-虚拟网络编辑器
选择NAT模式
![微信截图_20191024145922.png](https://i.loli.net/2019/10/24/xqiUHpKFXQrL5o3.png)

看看NAT设置网关Ip有没有设置
![微信截图_20191024150010.png](https://i.loli.net/2019/10/24/2RJEUeFKuYQrt1b.png)
![微信截图_20191024150018.png](https://i.loli.net/2019/10/24/x67v2iYPsHjopdV.png)

查看子网IP、子网掩码，这是默认设置的
![微信截图_20191024145956.png](https://i.loli.net/2019/10/24/mdAOvSyaYqwP963.png)

对你的操作系统的网络选择NAT模式
![微信截图_20191024150111.png](https://i.loli.net/2019/10/24/T91uiRYbwAINoBt.png)
#### 登陆系统  
输入``ifconfig``  
ens33 后面的inet后面就是这个系统的IP
![微信截图_20191024151303.png](https://i.loli.net/2019/10/24/PRkTaWCb1YmMvQV.png)
在windows系统使用cmd命令行
执行``ping 192.168.163.128``  
返回
```
正在 Ping 192.168.163.128 具有 32 字节的数据:
来自 192.168.163.128 的回复: 字节=32 时间<1ms TTL=64
来自 192.168.163.128 的回复: 字节=32 时间<1ms TTL=64
来自 192.168.163.128 的回复: 字节=32 时间<1ms TTL=64
来自 192.168.163.128 的回复: 字节=32 时间<1ms TTL=64

192.168.163.128 的 Ping 统计信息:
    数据包: 已发送 = 4，已接收 = 4，丢失 = 0 (0% 丢失)，
往返行程的估计时间(以毫秒为单位):
    最短 = 0ms，最长 = 0ms，平均 = 0ms
```
证明能够ping通
#### 安装wrk
首先安装两个工具
```
// 安装 make 工具
sudo apt-get install make

// 安装 gcc编译环境
sudo apt-get install build-essential
```
然后安装wrk
```
//克隆wrk安装文件到本地
git clone https://github.com/wg/wrk.git

//编译wrk文件
cd wrk && make
```
简单的demo
```
wrk -t2 -c10 -d15 --latency http://www.baidu.com
2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    35.64ms    1.31ms  48.59ms   79.38%
    Req/Sec   138.40     13.52   151.00     89.00%
  Latency Distribution
     50%   35.50ms
     75%   36.13ms
     90%   37.09ms
     99%   40.33ms
  4151 requests in 15.05s, 60.83MB read
  Socket errors: connect 0, read 29, write 0, timeout 0
Requests/sec:    275.72
Transfer/sec:      4.04MB
```
wrk参数含义
```
-t：需要模拟的线程数
-c：需要模拟的连接数
-d：测试的持续时间
--timeout：超时的时间
--latency：显示延迟统计
```
结果显示的指标
```
Latency：响应时间
Req/Sec：每个线程每秒钟的完成的请求数
Avg：平均
Max：最大
Stdev：标准差
```