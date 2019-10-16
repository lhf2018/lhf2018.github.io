---
title: 解决：'chromedriver' executable needs to be in PATH的问题
date: 2019-10-13 11:50:26
tags:
- selenium
- chrome
- python
categories:
- 开发工具
---
* 查看设置-帮助-关于Google Chrome中的版本，如77.0.3865.90
* 需要下载chrome的[webdriver](http://chromedriver.storage.googleapis.com/index.html)
找到对应的版本，下载
* 把webdriver放到chrome的安装文件夹中
```C:\Program Files (x86)\Google\Chrome\Application```
* 把webdriver也放入python的文件夹中
* 在环境变量的path中添加```C:\Program Files (x86)\Google\Chrome\Application```的环境变量

成功解决