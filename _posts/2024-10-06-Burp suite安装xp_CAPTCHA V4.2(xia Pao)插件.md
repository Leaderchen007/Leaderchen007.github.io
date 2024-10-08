---
title: Burp suite安装xp_CAPTCHA V4.2(xia Pao)插件
date: 2024-10-06 14:00:00 +0800
author: Pat1ence
categories: [Burp suite,Extensions]
tags: [Burp suite]


pin: false
math: true
mermaid: true
image:
  path: /imgs/1/1.png

---



# Burp suite安装xp_CAPTCHA V4.2(xia Pao)插件

项目地址：https://github.com/smxiazi/NEW_xp_CAPTCHA/releases

### 一、准备环境&注意

1、准备jdk1.8或者jdk16环境



2、如果是Windows系统且没有对应的python环境，可以下载作者在V4.1版本下发的打包好的环境（用起来很方便）



![img](https://raw.githubusercontent.com/Leaderchen007/Leaderchen007.github.io/refs/heads/main/imgs/1/2.png)



3、下载

![img](https://raw.githubusercontent.com/Leaderchen007/Leaderchen007.github.io/refs/heads/main/imgs/1/3.png)



### 二、启动服务

#### 1、替换python环境中的server.py

![img](https://raw.githubusercontent.com/Leaderchen007/Leaderchen007.github.io/refs/heads/main/imgs/1/4.png)

①把下载的<u>NEW_XP_CAPTCHA win64 python环境完整版.rar</u>解压，可以看到<u>NEW_xp_CAPTCHA/Python36/</u>目录下有一个server.py文件

②把<u>NEW_xp_CAPTCHA_4.2 .zip</u>解压，把server_4.2.py改名为server.py，然后替换掉Python36/目录下的server.py文件

#### 2、启动环境

点击<u>NEW_XP_CAPTCHA win64 python环境完整版/NEW_xp_CAPTCHA/</u>目录下的<u>运行.bat</u>，显示以下内容，并且访问http://127.0.0.1:8899，显示下图，表示环境启动成功。

~~~
Starting server, listen at: 0.0.0.0:8899
加载完成！请访问：http://127.0.0.1:8899
github:https://github.com/smxiazi/NEW_xp_CAPTCHA


欢迎使用ddddocr，本项目专注带动行业内卷，个人博客:wenanzhe.com
训练数据支持来源于:http://146.56.204.113:19199/preview
爬虫框架feapder可快速一键接入，快速开启爬虫之旅：https://github.com/Boris-code/feapder
~~~

![img](https://raw.githubusercontent.com/Leaderchen007/Leaderchen007.github.io/refs/heads/main/imgs/1/1.png)

## 三、在BP上安装插件

#### 1、整理文件

①这里建议创建一个无中文的目录（比如：NEW_xp_CAPTCHA_win64/），然后把<u>NEW_XP_CAPTCHA win64 python环境完整版/</u>目录下的<u>NEW_xp_CAPTCHA/</u>目录复制到我们创建的目录中。

②再把<u>NEW_xp_CAPTCHA_4.2 .zip</u>解压后目录下的NEW_xp_CAPTCHA_4.2.jkd.8.jar文件和NEW_xp_CAPTCHA_4.2.jkd.16.jar复制到我们创建的目录中。

#### 2、导入

在Burp Suite中依次点击[拓展]——[添加]——[选择文件]，选择NEW_xp_CAPTCHA_4.2.jkd.8.jar文件（这里本人用的jdk1.8所以导入对应的文件），直接点击[下一步]，看到successfully并且无报错，表示成功。

![img](https://raw.githubusercontent.com/Leaderchen007/Leaderchen007.github.io/refs/heads/main/imgs/1/5.png)

![img](https://raw.githubusercontent.com/Leaderchen007/Leaderchen007.github.io/refs/heads/main/imgs/1/6.png)



至此我们就把插件安装好辣！

~~客观稍等片刻，使用教程马上出炉~~



下面请欣赏泉哥的绝世容颜~~~



<iframe width="960" height="540" frameborder="0" src="https://open.douyin.com/player/video?vid=7422095296701746482&autoplay=0" referrerpolicy="unsafe-url" allowfullscreen></iframe>







