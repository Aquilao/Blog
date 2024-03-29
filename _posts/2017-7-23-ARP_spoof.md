---
layout: post
title: "ARP欺骗从入门到入狱"
date: 2017-7-23
excerpt: "实验：从ARP断网攻击到使用基于ARP欺骗的MITM窃取HTTPS加密的用户名和密码"
tag:
- Misc~
comments: true
---


## 实验环境
网关：无线路由器（192.168.0.1）  
攻击者kali linux 2.0（192.168.0.106）  
靶机：Android 6.0（192.168.0.101）

## 0x01-ARP断网攻击

由于这三个实验都是基于ARP欺骗的，所以我们首先要理解ARP大致工作流程

在要发送一个报文到某个IP地址时：  
1. 主机查找本机ARP缓存  
2. 如果没有，主机将广播包含目标IP地址的ARP请求报文  
3. 非目标主机丢弃报文，目标主机返回带有自己MAC地址的ARP应答报文  
4. 收到返回报文后主机将该IP地址和Mac地址存入本机ARP缓存中并保留一定时间(节约资源)

这里我们还不得不提一下ARP协议的两个最大的漏洞：  
1. 整个协议里不存在检测ARP报文真实性的部分  
2. 只要主机一接收到ARP报文就会马上在ARP缓存中替换掉原有正确的IP和Mac的映射关系  
所以我们可以随意的修改IP和Mac的映射关系，让靶机误以为攻击者是网关。  

好了，让我们开始实验。  
我们首先需要用到一个工具：arpspoof  
先让我们看看它到底该怎么用吧

    $ sudo arpspoof -h

得到版本号和参数

    Version: 2.4
    Usage: arpspoof [-i interface] [-c own|host|both] [-t target] [-r] host     （-i所指的接口就是你当前使用的网卡）

知道它需要哪些参数就好办了，本机目前使用的网卡是wlan0，我的内网IP是192.168.0.106，靶机的内网IP是192.168.0.101（当然在真实环境中没这么容易获得目标的IP的，一般是需要先欺骗再进行数据包分析以确定目标IP，所以说数据包分析是前置技能，可惜本菜鸡还没到这水平～）

    $ sudo arpspoof -i wlan0 -t 192.168.0.101 192.168.0.1

用这条命令攻击的过程大概来说就是对192.168.0.101发送ARP报文，报文中192.168.0.1对应的Mac地址被替换成了192.168.0.106的Mac地址，告诉它攻击者是网关，所以靶机就会把本该发给网关的数据包发给攻击者，表现出来的现象就是断网。

我们这个时候可以试试用靶机ping百度，ping不通，浏览器里也打不开非本地的网站，证明实验成功了。


## 0x02-使用基于ARP欺骗的MITM窃取HTTP报文中的用户名和密码

首先，我们必须知道一些关于HTTP的知识

客户端和Web服务器HTTP通信过程：  
1. 客户端与Web服务器进行三次握手，建立TCP链接  
2. 客户端向Web服务器的80端口发送HTTP请求
3. Web服务器向客户端发送HTTP响应  
4. 客户端与Web服务器进行四次握手，断开TCP链接

HTTP有一个特性在本实验里特别重要：HTTP里的内容是**明文传输**的（划重点）

接下来我们介绍一下攻击思路  
MITM的前提就是攻击者要作为中间人存在于靶机和与靶机进行通讯的计算机之间，在这个实验里我们的目的是窃取HTTP报文里面的帐号密码，所以我们直接让攻击者处于靶机和网关之间，一切出网关的报文都会经过攻击者。不幸的是HTTP报文里面的所有内容都是明文传输的，只要抓到包含帐号密码的HTTP报文，我们可以直接在里面看到用户名和密码，所以现在推行的HTTPS就是为了防止骇客直接从HTTP报文里找到你的帐号密码以及其他重要信息而用SSL加密的HTTP，我会在下个实验里面更加具体讲解。

刚刚已经说明了攻击思路，现在我们要来实现它了。  
首先我们需要让攻击者处于靶机和网关之间，这就需要先进行ARP欺骗来让靶机误以为攻击者就是网关。但是还有一个部分就是在通过攻击者主机的报文中，抓取到带有帐号密码的HTTP报文，该怎么办呢？用wireshark来抓包？这的确可行，但是在真实场景里一台计算机所产生的数据包简直是海量的，从里面找出包含帐号密码的HTTP报文需要花大量的时间和精力，黑客们肯定不会这么做，他们开发出了Ettercap使其自动化（当然Ettercap的功能不限于此，但因为本文主题我就不具体介绍了）

我们用Arpspoof我们继续上个实验的操作：

    $ sudo arpspoof -i wlan0 -t 192.168.0.101 192.168.0.1

这就实现了让靶机把攻击者当作网关的部分，但是我们还记得在第一个实验里，这条命令造成的结果是断网。这其实是攻击者没有开启路由转发功能，报文虽然到了攻击者但是没被攻击者交给网关，因为报文的IP地址并不是属于它的，所以被丢弃了。开启路由转发功能的攻击者如果只看协议栈的下三层的话，可以视为一台路由器。

所以在输入上一条命令前，我们要先开启路由转发功能  

    # echo 1 > /proc/sys/net/ipv4/ip_forward

我们可以用cat查看/proc/sys/net/ipv4/ip\_forward这个文件，默认是0（关闭）我们执行了这条命令后它就会变为1（开启）。这个文件在电脑关闭后会重置。

欺骗成功后我们就要使用Ettercap获取帐号密码了

    $ sudo ettercap -T -q -i wlan0

帮助文档里的参数比较多，我就稍微介绍这几个：-T：选择GUI为命令行，-q：安静模式（不显示报文内容，只能在GUI命令行时使用），-i：端口（也就正在使用的网卡）

这条命令能从wlan0上的报文中嗅探并显示出一些比较重要的报文，我们的目标：包含用户名和密码的HTTP报文当然也在其中。

我们在靶机上用武汉大学（随便找的，无恶意~）的教务系统做测试一下，输入用户名admin密码password
Ettercap马上就返回了以下的数据

    0    CONTENT: username=admin&password=password&captchaResponse=&lt=LT-1951166-WzPj5tTMtB7fy2DdJBMmKdjykjBSdI1500909066555-mrXp-cas&dllt=userNamePasswordLogin&execution=e1s2&_eventId=submit&rmShown=1

用户名和密码还有登录的URL都在其中，实验成功。

所以说用HTTPS来淘汰掉HTTP是很有必要的，但是HTTPS也不是我们想象的那么安全。下一个实验我们就要窃取HTTPS中的用户名和密码了。

## 0x03-使用基于ARP欺骗的MITM窃取HTTPS报文中的用户名和密码

既然这个实验也是属于MITM，其中的思路和上一个一模一样，我就不再说了，但我们要知道一些关于HTTPS的知识。

如果要用一句话来概括HTTPS的话，那就是:

> HTTPS协议是由HTTP+SSL/TLS构建的可进行加密传输、身份认证的网络协议

HTTPS的通信过程和HTTP略有不同：  
1. 客户端与Web服务器进行三次握手，建立TCP链接  
2. 客户端向Web服务器的80端口发送HTTP请求  
3. Web服务器向客户端发送应答状态码为302的HTTP响应，把基于HTTP传输的网站重定向到基于HTTPS的同一个网站  
4. 客户端向Web服务器的443端口发送HTTPS请求  
5. Web服务器向客户端发送包含数字签名的证书（关于数字签名证书什么的可以去看看这个：[数字签名是什么？](http://www.ruanyifeng.com/blog/2011/08/what_is\_a\_digital\_signature.html)）  
6. 客户端验证证书  
7. 建立基于SSL/TLS的加密通信（失败的话就会提示证书过期或不可信）  
8. 客户端与Web服务器进行四次握手，断开TCP链接

看出什么了吗？  
HTTPS之所以安全的原因就是它把数据用SSL加密了，但要是我们在SSL/TLS加密之前做一些手脚的话，HTTPS就不再安全了。  
在第三步，只要不让客户端知道Web服务器想要把它重定向到HTTPS的网站，它就会继续向Web服务器发送HTTP报文，而只要让Web服务器以为和客户端正常通信的话在这部分一丝痕迹都不会泄漏。但是我们怎么能做到这些呢？别忘了我们的攻击者作为中间人处于Web服务器和靶机之间，我们可以通过把靶机发往攻击者的HTTP报文修改成HTTPS再发送给Web服务器，这样在Web服务器看来这只是一次正常的和客户端通信而已。这个原理和小时侯老师让你交50学杂费然而你找老妈要了100是一模一样的，但不一样的是Web服务器和靶机不会有一天碰在一起发现了真相然后把你打一顿。

我们要启用Kali里自带的一个工具:SSL strip来实现这次攻击  
在使用之前我们需要先修改一下Ettercap的配置文件

    $ sudo vim /etc/ettercap/etter.conf

找到如下的部分，把# if you use iptables:下面两个条目前的#去掉

    #---------------
    #     Linux
    #---------------

    # if you use ipchains:
       #redir_command_on = "ipchains -A input -i %iface -p tcp -s 0/0 -d 0/0 %port -j REDIRECT %rport"
       #redir_command_off = "ipchains -D input -i %iface -p tcp -s 0/0 -d 0/0 %port -j REDIRECT %rport"

    # if you use iptables:
       #redir_command_on = "iptables -t nat -A PREROUTING -i %iface -p tcp --dport %port -j REDIRECT --to-port %rport"
       #redir_command_off = "iptables -t nat -D PREROUTING -i %iface -p tcp --dport %port -j REDIRECT --to-port %rport"

现在可以开始使用SSL strip了

    $sudo sslstrip -a -f -k

（-a记录所有的SSL和HTTP，-f代替锁图标的安全需求，-k中断在进行的会话）

我们这次用4399试试
在登录框输入用户名ilove4399，密码password  
Ettercap返回了如下数据：

    HTTP : 120.147.161.50:443 -\> USER:   PASS: password  INFO: https://ptlogin.4399.com/oauth2/loginAndAuthorize.do
    CONTENT: isInputRealname=false&isValidRealname=false&username=ilove4399&password=password&uid=&auth\_action=LOGIN&scope=basic&show\_close\_button=&username\_history=&redirect\_uri=http%3A%2F%2Fsave.api.4399.com%2Fh5%2Fv2%2Fuser%2Fcallback%3Ffrom%3Dhttp%3A%2F%2Fh.4399.com%2Fuser.htm&show\_4399=&show\_back\_button=&show\_topbar=&autoCreateAccount=&aid=&_d=&cid=&reg_mode=reg\_phone&show\_ext\_login=true&expand\_ext\_login\_list=&ref=&response\_type=TOKEN&show\_forget\_password=&auto\_scroll=&client\_id=a9a16636dbaeb917e2ffb16f0d52006e&css=&access\_token=

出了点小问题，用户名没有在上面显示出来，但我们能下面的数据里面找出，所以无伤大雅。


**受本菜鸡水平所影响，这篇文章里面可能有不少错误，如有发现请发Email指正，多谢！**
