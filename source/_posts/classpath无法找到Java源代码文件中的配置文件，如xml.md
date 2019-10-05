---
title: classpath无法找到Java源代码文件中的XML等配置文件
date: 2019-10-05 00:15:26
tags:
- maven
- idea
categories:
- maven
---
#### 问题
今天在使用mybatis时，程序找不到Java源代码mapper文件夹中映射用的mapper.xml   
```
<property name="mapperLocations" value="classpath:com/withstars/mapper/*.xml" />
```
这是无效的，当我们去项目生成的classes文件夹中寻找时，发现找不到xml文件夹  
思考查阅发现，IDEA默认不会在编译时把Java源代码文件中的XML等资源文件打包到classes文件夹中，只会把resources文件夹的资源文件打包到classes
* classpath：只会到你的class路径中查找找文件。
* classpath\*：不仅包含class路径，还包括jar文件中（class路径）进行查找。

当我们使用classpath时，它寻找的路径是已经生成的classes文件夹，而不是Java源代码文件夹（这是一个关键的概念），因为classes文件夹没有打包xml，所以自然找不到

#### 解决办法
下面提供两种方法：

1. 把mapper/\*.xml复制到resources下面
2. 如果是maven项目，则在pom的&lt;build&gt;中添加
```
<resources>
  <!-- mapper.xml文件在java目录下 -->
  <resource>
    <directory>src/main/java</directory>
    <includes>
      <include>**/*.xml</include>
    </includes>
  </resource>
</resources>
```
