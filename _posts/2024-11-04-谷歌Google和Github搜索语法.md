---
title: Google&Github搜索语法总结
date: 2024-11-04 19:00:00 +0800
description: 本文总结了Google和Github的一些搜索语法。
categories: [Penetration,Information]
tags: [Information,Grammar]


pin: false
math: true
mermaid: true
image:
  path: /imgs/2/B466B4071D30D21F7562D5FB6910C627.gif
---



## 一、谷歌语法

利用Google Hacking的语法，精准的搜索某些网址的主机漏洞，构造特殊关键字来搜索互联网上的相关敏感信息。

### 1、逻辑

#### (1) AND

#### (2) OR

#### (3) 双引号""

不可分割

~~~
"adminpassword"
~~~

希望搜索“adminpassword”的内容，而不希望这两个单词之间有其它内容。

#### (4) 减号-

排除一些不想要的搜索结果

E.g. ：马达-加斯加。搜索马达，排除加斯加。



### 2、搜索

#### (1) site

搜索某个域名下的**所有页面**

~~~
Site:family.chinaok.com
~~~

返回所有和这个网站有关的URL。

#### (2) inurl

搜索包含有特定字符的**URL**。

~~~~
inurl:login.php  
~~~~

可以找到带有"login.php"字符的URL。

#### (3) allinurl

搜索包含多个单词的网址

~~~
allinurl:red buildings
~~~

#### (4) intitle

返回所有**网页标题**中包含关键词的网页。

~~~
intitle:管理
~~~

这样网页标题中带有“管理”的网页都会被搜索出来

#### (5) allintitle

#### (6) intext

搜索**网页正文**内容中的指定字符

~~~
intext:cbi
~~~

将返回所有在网页正文部分包含 cbi 的网页。

#### (7) allintext

搜索正文内容中包含**多组关键词**的页面

#### (8) link

~~~
link:thief.one
~~~

返回所有和 thief.one 做了**链接**的URL。

#### (9) filetype

搜索指定类型的**文件**。

~~~
filetype:mdb
~~~

将返回所有以“mdb”结尾的文件URL。

#### (10) related

~~~
related:baidu.com
~~~

**只适用于 Google 搜索引擎**，搜索与某个网站有关联的页面，可能是基于拥有的共同外链判断

#### (11) around(X)

临近搜索，表示两个搜索关键词中间最多**相邻 X 个词**。

~~~
China AROUND(4) project 
~~~

China 和 project 中间可以是一个词、两个词、三个词或者四个词。

#### (12) `.`

单一的通配符。

#### (13) `*`

通配符，可代表多个字母。

#### (14) inanchor

用来搜索**导入链接锚文本中包含搜索词的页面**，简单来说就是我们看到的可以点的文字（[15个常用的高级搜索指令，和10个谷歌搜索技巧 - DMthought](https://dmthought.com/advanced-search/)）

~~~
inanchor:China manufacturer
~~~

这里的搜索结果未必会包含 China manufacturer 这个关键词，但是指向它们的外链锚文本中会包含 China manufacturer 这个关键词

#### (15) 波浪号~

获得目标关键词及其**近似词**的搜索结果

~~~
SEO ~教程
~~~

查找SEO方面的策略或者教程

#### (16)两个点..

用两个句点分隔数字，不带空格，以**搜索该范围内的数字**

~~~
"计算机里程碑" 1950..2000
~~~

查找1950年至2000年间发生的计算机里程碑。

**去掉1950将查找2000年之前的数字**

### 3、用法示例

①想要获取高校的管理后台

~~~
site:edu.cn AND intitle:后台 OR intitle:管理 

site:edu.cn AND OR inurl:login.asp OR admin.asp
~~~

②寻找目标网站的数据库

~~~
site:target.com AND filetype:mdb
~~~

③搜索C段服务器信息

~~~
site:127.0.0.*
~~~

④上传漏洞

~~~
site:target.com AND inurl:upload.asp or inurl:upload_soft.asp
~~~

### 4、Hacker语句

[Google Hacking Database (GHDB) - Google Dorks, OSINT, Recon](https://www.exploit-db.com/google-hacking-database)

如果扫描器扫到的网站管理人员的信息（账号，邮箱），可以根据信息搜索

~~~
site:github.com smtp 
site:github.com smtp @qq.com
site:github.com smtp @126.com
site:github.com smtp @163.com
site:github.com smtp @sina.com.cn
site:github.com smtp password
site:github.com string password smtp
~~~



## 二、Github语法

### 1、按照项目/仓库名称搜索

~~~
in:name XXX
~~~



### 2、仓库描述关键词

~~~
in:descripton XXX
~~~



### 3、README文件描述搜索

~~~
in:readme XXX
~~~



### 4、stars数量

~~~
stars:>3000 XXX   //stars数量大于3000

stars:1000..3000 XXX    //stars数量大于1000小于3000
~~~



### 5、fork数限制搜索

~~~
forks:>1000 XXX   //forks数量大于1000的

forks:1000..3000 XXX   //orks数量大于1000小于3000
~~~



### 5、发布时间

~~~
pushed:>2019-02-12 XXX  //发布时间大于2019-02-12
~~~



### 6、创建时间

~~~
created:>2019-02-12 XXX   //创建时间大于2019-02-12
~~~

### 7、用户名

~~~
user:XXX
~~~

### 8、某用户名下的所有仓库/项目

~~~
org:username

org:username objectname     //某用户名下的某个项目/仓库
~~~



### 9、编程语言

~~~
language:java XXX
~~~

### 10、关注者

~~~
node followers:>=10000   //匹配有 10,000 或更多关注者提及文字 "node" 的仓库
~~~

### 11、仓库大小

~~~
size:>=5000 XXX  //仓库大于5000kb
~~~

### 12、排除特定结果

~~~
hello NOT world   //匹配含有 "hello" 字样但不含有 "world" 字样的仓库。
~~~

### 13、对带有空格的查询使用引号

~~~
org:"admin password"
~~~

