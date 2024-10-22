---
title: Fuzz工具
date: 2024-10-21 20:00:00 +0800

description: 本文是对两个用于Fuzz工具Trubo Intruder和ffuf使用方法的介绍。
categories: [Fuzz,Tools]
tags: [Fuzz]


pin: ture
math: true
mermaid: true
image:
  path: /imgs/Article_cover/20241021_205217.png
---

## 一、Trubo Intruder

### 1、介绍

Turbo Intruder 是一个 BurpSuite 插件，用于发送大量HTTP请求并分析结果。它的设计目的是补充 Intruder 的不足。

**特点：**

<div class="box-info" markdown="1">
快速 -Turbo Intruder 使用了一个重写的 HTTP 栈 ，用于提升速度。在许多目标上，它甚至可能超过流行的异步 Go 脚本。
可扩展 -Turbo Intruder 运行时使用很少的内存，从而可以连续运行几天。同时可以脱离 burpsuite 在命令行下使用。
灵活 - Turbo Intruder 的攻击是使用 Python 配置的。这样可以处理复杂的要求，例如签名的请求和多步攻击序列。此外，自定义 HTTP 栈意味着它可以处理其他库无法处理的畸形格式请求。
方便 - 它的结果可以通过 Backslash Powered Scanner 的高级差异算法自动过滤。这意味着您可以单击两次即可发起攻击并获得有用的结果。
</div>

**优点：**

<div class="box-info" markdown="1">
- 快速 - 使用从头开始手动编码的 HTTP 堆栈，同时考虑到速度。因此，在许多目标上，它甚至可以严重超过时尚的异步 Go 脚本。
- 可扩展 - 可以实现固定内存使用，从而实现可靠的多日攻击。它也可以通过命令行在无头环境中运行。
- 灵活 - 使用 Python 配置攻击。这样可以处理复杂的要求，例如签名请求和多步骤攻击序列。此外，自定义 HTTP 堆栈意味着它可以处理破坏其他库的格式错误的请求。
- 方便 - 可以通过适用于 Backslash Powered Scanner 的高级差异算法自动过滤掉无聊的结果。这意味着您只需单击两次即可发起攻击并获得有用的结果。
</div>

### 2、使用

①`Send to turbo intruder`之后

![image-20241019234642508](https://raw.githubusercontent.com/Leaderchen007/Leaderchen007.github.io/refs/heads/master/imgs/1/image-20241019234642508.png)

分为上下两个模块，要挂字典处插入`%s`

#### 调节速度

①配置介绍

![image-20241020000315522](https://raw.githubusercontent.com/Leaderchen007/Leaderchen007.github.io/refs/heads/master/imgs/1/image-20241020000315522.png)

②开始时，可以把`concurrentConnections` 设置为 1 ，`requestsPerConnection` 设置为 100，测试单个连接的最大请求量

<div class="box-tip" markdown="1">
>Reqs：请求数
>Duration：使用时间

>RPS：每秒请求数量
</div>


<div class="box-warning" markdown="1">
<div class="title"> 这里要注意的是RPS和Retires</div>
- 如果 Retries 过多，说明有很多请求失败，需要调低点同时发送的请求。
- 可以把线程调大或调小，看RPS的变化，如果变大可以采用
</div>

![image-20241020011700739](https://raw.githubusercontent.com/Leaderchen007/Leaderchen007.github.io/refs/heads/master/imgs/1/image-20241020011700739.png)

③把 pipeline 设置为 True，concurrentConnections 设置成5

根据RPS和Retires的变化来选中合适的并发连接数

<div class="box-tip" markdown="1">
<div class="title"> 提示 </div>
以RPS越大、Retires越小，工具效果越好
</div>



### 3、进阶使用

#### (1)测试并发漏洞

默认脚本使用流式攻击方式，这对于最大程度地减少内存使用量非常有用，但对于查找竞争条件而言并不是理想的选择。要查找竞争条件，您将要确保所有请求都在尽可能小的窗口内命中目标，这可以通过在启动请求引擎之前对所有请求进行排队来完成。

可以在下面地址找到代码示例：

<https://github.com/PortSwigger/turbo-intruder/tree/master/resources/examples/race.py>

~~~python
1 def queueRequests(target, wordlists):
2     engine = RequestEngine(endpoint=target.endpoint,
3                            concurrentConnections=30,
4                            requestsPerConnection=100,
5                            pipeline=False
6                            )
7 
8 # the 'gate' argument blocks the final byte of each request until openGate 9 is invoked
9 	for i in range(30):
10         engine.queue(target.req, target.baseInput, gate='race1') 
11 #“gate”参数会阻塞每个请求的最后一个字节，直到调用openGate
12 # wait until every 'race1' tagged request is ready
13 # then send the final byte of each request
14 # (this method is non-blocking, just like queue)
15    engine.openGate('race1')
16 
17    engine.complete(timeout=60)
18 
19 
20 def handleResponse(req, interesting):
21     table.add(req)
~~~

**代码分析：**

<div class="box-tip" markdown="1">
- 这段代码并发测试的时候，不是使用队列来发送数据的。
- 代码同时开启了30个连接
- 第10行的gate用来标识属于同一个并发测试的请求
- 此时，会在30个连接中各放入一个请求，但会留下最后一个字节不发送出去，然后在第 15 行处等待30个连接中的请求都准备好后一起发送最后一个字节，这样服务器就会同时处理30个请求，从而达到了并发测试的目的。
- 第17行设置最迟60秒后显示结果
</div>

<div class="box-tip" markdown="1">
<div class="title">  提示 </div>
第9行处的30必须和` concurrentConnections`值一样
</div>

**上面的代码已经更新为（方便分析所有保留旧代码）：**

~~~python
def queueRequests(target, wordlists):

    engine = RequestEngine(endpoint=target.endpoint,

                           concurrentConnections=20,

                           requestsPerConnection=30,

                           pipeline=False

                           )

    for i in range(20):        #创建30个请求，需要和concurrentConnections对应，每条连接发送一个请求

        engine.queue(target.req, target.baseInput, gate='race1')    #“gate”参数会阻塞每个请求的最后一个字节，直到调用openGate

#等待，直到每个“race1”标记的请求就绪，然后发送每个请求的最后一个字节

    engine.openGate('race1')    #标识属于同一个并发测试的请求

    engine.complete(timeout=60)

def handleResponse(req, interesting):

    table.add(req)

~~~

#### - 利用

**短信并发、优惠券并发、下单并发、支付并发、删除并发**、**文件上传**都可以并发

#### (2)爆破账号密码

①爆破密码②爆破账号③同时爆破账号密码

<div class="box-warning" markdown="1">
<div class="title"> 注意 </div>
使用一个模式就注释掉其他模式；字典的更改；同时爆破账号密码时，记得都要添加%s
</div>

~~~python
from urllib import quote

def password_brute(target,engine):
  for word in open(r'D:\Bolg\top1000.txt'):
        engine.queue(target.req, quote(word.rstrip()))

def user_brute(target,engine):
  for word in open(r'D:\Bolg\top1000.txt'):
        engine.queue(target.req, quote(word.rstrip()))
 
def user_password_brute(target, engine):
  for password in open(r'D:\Bolg\top1000.txt'):
    for user in open(r'D:\Bolg\top1000.txt'):
            engine.queue(target.req， [quote(user.rstrip()),quote(password.rstrip())])

def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint,
            concurrentConnections=30,
            requestsPerConnection=100,
            pipeline=False
            )
    #user_brute(target,engine)
    #password_brute(target,engine)
    user_password_brute(target,engine)

def handleResponse(req, interesting):
# currently available attributes are req.status, req.wordcount, req.length and req.response
    if req.status == 302:
       table.add(req)
~~~

#### (3)爆破验证码

~~~python
from itertools import product

def brute_veify_code(target, engine, length):
    pattern = '1234567890'
    for i in list(product(pattern, repeat=length)):
         code =  ''.join(i)
         engine.queue(target.req, code)


def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint,
            concurrentConnections=30,
            requestsPerConnection=100,
            pipeline=True
            )
    brute_veify_code(target, engine, 6) #根据验证码位数修改


def handleResponse(req, interesting):
# currently available attributes are req.status, req.wordcount, req.length and req.response
  if 'error' not in req.response:
       table.add(req)
~~~

例子中为6位的验证码，模式位纯数字

#### (4)对多个目标目录扫描

~~~python
def mult_host_dir_brute():
    req = '''GET /%s HTTP/1.1
Host: %s
Connection: keep-alive

'''
    engines = {}
for url in open(r'D:\Bolg\urls.txt'):
        url = url.rstrip()
        engine = RequestEngine(endpoint=url, 
            concurrentConnections=5,
            requestsPerConnection=100,
            pipeline=True)
        engines[url] = engine
        
for word in open(r'D:\Bolg\top1000.txt'):
        word = word.rstrip()
for (url, engine) in engines.items():
            domain = url.split('/')[2]
            engine.queue(req, [word, domain])

def queueRequests(target, wordlists):
    mult_host_dir_brute()

def handleResponse(req, interesting):
# currently available attributes are req.status, req.wordcount, req.length and req.response
  if req.status != 404:
       table.add(req)
~~~

<div class="box-tip" markdown="1">
<div class="title"> 提示 </div>
urls.txt里面存放URL
</div>

参考文章：[Turbo Intruder 使用 - 拥抱十亿请求攻击-CSDN博客](https://blog.csdn.net/qq_28205153/article/details/113832488)

## 二、ffuf

### 1、介绍

- 基于 Go 语言开发，速度极快，并且跨平台
- 支持颜色高亮输出
- 有强大的过滤系统和代理系统
- 适应多种情景下的模糊测试
- 最重要的是它可操作性比较强，功能可以涵盖目录扫描、DNS 扫描、Payload 测试等等，我们只需要改变 FUZZ 参数的位置+高质量的字典即可。所以用好这一款工具就可以代替比如像 Dirsearch、御剑这种功能比较单一的工具。

### 2、使用

#### (1)基础指令

~~~
-u url地址
-w 设置字典
-c 将响应状态码用颜色区分，windows下无法实现该效果。
-t 线程率，默认40
-p 请求延时： 0.1、0.2s
-ac 自动校准fuzz结果
-H Header头，格式为 “Name: Value”
-X HTTP method to use
-d POST data
-r 跟随重定向
-recursion num 递归扫描
-x 设置代理 http 或 socks5://127.0.0.1:8080
-s 不打印附加信息，简洁输出
-e 设置脚本语言 -e .asp,.php,.html,.txt等
-o 输出文本
-of 输出格式文件，支持html、json、md、csv、或者all
~~~

#### (2)基本使用

①单个字段

~~~
./ffuf -u http://testphp.vulnweb.comFUZZ -w ../fuzzDicts/directoryDicts/top7000.txt
~~~

②多个字段，一个字典

~~~
./ffuf -u http://testphp.vulnweb.comFUZZ1\?FUZZ2 -w ../fuzzDicts/directoryDicts/top7000.txt
~~~

③多个字段，多个字典

~~~
ffuf -u http://192.168.111.132/sqli/example1.php?FUZZ1=FUZZ2 -w ../fuzzDicts/directoryDicts/params.txt:FUZZ1 -w ../fuzzDicts/directoryDicts/top3000.txt:FUZZ2
~~~

④加Cookie扫描

~~~
./ffuf -u http://testphp.vulnweb.comFUZZ -w ../fuzzDicts/directoryDicts/top3000.txt -b "Cookie=xxx"
~~~

⑤静默模式 

`-s`

（只输出结果，不打印附件信息）

~~~
./ffuf -u http://testphp.vulnweb.comFUZZ -w ../fuzzDicts/directoryDicts/top3000.txt -s
~~~

⑥递归扫描 

`recursion`

~~~
./ffuf.exe -u http://192.168.111.130/FUZZ -w ../fuzzDicts/directoryDicts/top3000.txt -recursion 2
~~~

<div class="box-danger" markdown="1">
<div class="title"> 注意 </div>
字典内开头不要有 / ，否则不会进行递归扫描
</div>



<div class="box-tip" markdown="1">
<div class="title"> 提示 </div>
一起使用`-maxtime-job`与`-recursion`递归扫描，用于指定每个目录递归扫描时间，避免扫描时间过长。
</div>



~~~
./ffuf.exe -u http://192.168.111.130/FUZZ -w ../fuzzDicts/directoryDicts/top3000.txt -recursion 2 -maxtime-job 10
~~~

⑦指定扩展名

`-e`

~~~
./ffuf -u http://192.168.111.130/FUZZ -w ../fuzzDicts/directoryDicts/top3000.txt -e .php,.zip
~~~

⑧POST方式爆破

~~~
./ffuf -request test.txt -request-proto http -mode clusterbomb -w ../fuzzDicts/directoryDicts/top3000.txt
~~~

<div class="box-tip" markdown="1">
<div class="title"> 参数分析 </div>
1、-mode 爆破模块，目前有clusterbomb、 pitchfork两个模式具体，类似burpsuite的爆破模块
- 在clusterbomb模式下，用户名单词列表中的每个单词都将与密码单词列表中的每个单词组合使用。就像如果列表 1 中有 4 个单词而列表 2 中有 5 个单词，那么总共会有 20 个请求。（集束炸弹模式）
- 在pitchfork模式下，用户名列表中第一个单词将与密码列表中第一个单词一起使用，同样，用户名列表中第二个单词将与密码列表中第二个单词一起使用。如果两个列表中的单词数量不同，那么一旦单词数量较少的列表耗尽就会停止。
2、-request标志可用于指定与原始HTTP请求文件，并且将相应使用FUZZ
3、-request-proto与原始请求一起使用的协议（默认值：https）
</div>



⑨修改User-Agent

`-H`+UA头

原因：Fuff的UA头容易被安全设备识别

~~~
ffuf -u http://192.168.111.130/FUZZ -w ./ffuf -request test.txt -request-proto http -mode clusterbomb -w ../fuzzDicts/directoryDicts/top3000.txt -c -H "User-Agent: Mozilla/5.0 Windows NT 10.0 Win64 AppleWebKit/537.36 Chrome/69.0.3497.100"
~~~

⑩随机User-Agent

借助了工具randomua

项目地址：<https://github.com/picatz/randomua>

使用方法：<https://blog.raw.pm/en/randomua/>

~~~
ffuf -u http://127.0.0.1/FUZZ -w ../Dictionary/fuzzDicts/directoryDicts/top7000.txt -H  "User-Agent: $(randomua -d)"
~~~

#### (3)子域名爆破

kali自带的一些子域名字典：

~~~
locate dns | grep "/usr/share" | grep ".txt"
~~~

命令：（主要是Host中的FUZZ）

~~~
ffuf -w ../Discovery/DNS/bitquark-subdomains-top100000.txt -u http://shoppy.htb -H "Host:FUZZ.shoppy.htb" -fs 169
~~~

#### (4)保存结果

- 保存为1.csv

~~~
ffuf -u http://www.baidu.com/FUZZ -w ../Dictionary/fuzzDicts/directoryDicts/top7000.txt -of csv -o 1.csv
~~~

- 保存为1.html

~~~
ffuf -u http://www.baidu.com/FUZZ -w ../Dictionary/fuzzDicts/directoryDicts/top7000.txt -of html -o 1.html
~~~

#### (5)匹配响应内容

①匹配状态码（匹配200.301）

~~~
ffuf -u http://192.168.111.130/FUZZ -w ../Dictionary/fuzzDicts/directoryDicts/top7000.txt -c -mc 200,301
~~~

②匹配行数

~~~
-ml 8
~~~

响应体和响应头之间的空格算一行

③字数字数

~~~
-ms
~~~

④按正则表达式匹配

~~~
-mr "phpmyadmin"
~~~

#### (6)过滤响应内容

①过滤状态码

~~~
-fc
~~~

②过滤响应包行数

~~~
-fl
~~~

③过滤字数

~~~
-fw
~~~

④过滤大小

~~~
-fs
~~~

⑤按正则表达式过滤

~~~
fr "phpmyadmin"
~~~

#### (7)其他参数

①颜色输出

~~~
-c
~~~

②设置线程

~~~
-t 50
~~~

③请求延时

~~~
-p 0.1
~~~

④跟随重定向

~~~
-r
~~~

⑤超时时间

~~~
-timeout 2
~~~

⑥详细测试

~~~
-v
~~~

⑦代理

~~~
-x http://127.0.0.1:8080
~~~

⑧任务最大时间

~~~
-maxtime 60
~~~

⑨忽略字典的注释信息

~~~
-ic
~~~

