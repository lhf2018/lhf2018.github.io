---
title: springboot无法访问jsp页面的一种解决办法
date: 2019-11-24 22:41:12
tags:
- springboot
categories:
- springboot
---
当使用springboot进行 对jsp页面的访问时，发现总是显示404界面
在添加了依赖
```
<dependency>
 
           <groupId>org.apache.tomcat.embed</groupId>
 
           <artifactId>tomcat-embed-jasper</artifactId>
 
           <scope>provided</scope>
 
       </dependency>
```
后依然无法访问，这是可以考虑把webapp中的文件放到resources文件夹下，
也就是  
``resources/META-INF/resources``  
##### 如图
![微信截图_20191124225004.png](https://i.loli.net/2019/11/24/Ko1ceXx46FlfmDp.png)