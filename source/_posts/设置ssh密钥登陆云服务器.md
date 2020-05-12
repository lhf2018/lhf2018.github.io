---
title: 设置ssh密钥登陆云服务器
date: 2020-02-15 12:20:19
tags:
- linux
categories:
- Linux
---

使用密码登陆远端云服务器，进入~/.ssh
执行：
```
ssh-keygen
```
```
Enter file in which to save the key (/home/eai/.ssh/id_rsa):    <====== enter 确认
Enter passphrase (empty for no passphrase):    <====== 输入密钥密码锁，或者直接enter默认无密码
Enter same passphrase again:    <====== 再次输入密钥密码锁
```
生成了两个文件：id_rsa(私钥），id_rsa.pub(公钥)  

把公钥写入服务器
```
cat id_rsa.pub >> authorized_keys
```
设置一下文件的权限
```
chmod 600 authorized_keys
chmod 700 ~/.ssh 
```
启用密钥验证
```
vi /etc/ssh/sshd_config
```
保证对应处为yes
```
#启用密钥验证
RSAAuthentication yes
PubkeyAuthentication yes
#指定公钥数据库文件
AuthorsizedKeysFile.ssh/authorized_keys
```
关闭密码登陆
```
vi /etc/ssh/sshd_config
```
```
#PasswordAuthentication yes 改为
PasswordAuthentication no
```
重启ssh
```
systemctl restart sshd.service
```

查了一下ssh密钥登陆的原理

摘自[图解SSH原理](https://www.jianshu.com/p/33461b619d53)

1. Client将自己的公钥存放在Server上，追加在文件authorized_keys中。  
2. Server端接收到Client的连接请求后，会在authorized_keys中匹配到Client的公钥pubKey，并生成随机数R，用Client的公钥对该随机数进行加密得到pubKey(R)，然后将加密后信息发送给Client。  
3. Client端通过私钥进行解密得到随机数R，然后对随机数R和本次会话的SessionKey利用MD5生成摘要Digest1，发送给Server端。  
4. Server端会也会对R和SessionKey利用同样摘要算法生成Digest2。  
5. Server端会最后比较Digest1和Digest2是否相同，完成认证过程。  

##### 总的来说就是非对称加密的相关原理

相关文章[RSA算法原理（一）](https://www.ruanyifeng.com/blog/2013/06/rsa_algorithm_part_one.html)