---
layout: post
title:  "锐捷易网关渗透测试"
date:   2017-11-5
excerpt: "锐捷易网关 guest 默认口令 + RCE + 垂直越权，可提权"
tag:
- Pentest
comments: true
---

上个月和ihexon开始玩学校的网,发现了台易网关有 guest 默认口令 + RCE + 垂直越权，玩了玩之后就修复了，简直网络义工（逃。而且写了份报告提交给了学校，现在这台网关的web服务已经关闭，一些敏感信息已经修改，所以把这篇报告放Blog里。另外由于图片分辨率较大，缩放会显得比较模糊，所以请放大或查看原图。

## Elevation_of_privilege-Ruijie_gateway


## 0x00 声明

我们只是为学校网络进行测试，而且此漏洞已被我们义务修复，造成无法复现。
由于当时并未保存所有图片，我们在报告中会使用其他同类型设备的同类型漏洞截图。
此报告可能暴露学校的部分内网结构以及一些敏感信息，请谨慎传播！


## 0x01 发现

- 路由追踪

    Aquilao@Agnes:~$ traceroute www.baidu.com

逐跳用Nmap扫描，数据包第三跳出内网，可知第三跳IP可能为出口网关

    Aquilao@Agnes:~$ nmap -A 172.17.1.1

8888端口打开，运行HTTPD服务，推测这应该是一个web管理界面。

![Alt text](https://github.com/Aquilao/Blog/raw/master/assets/img/Elevation_of_privilege-Ruijie_gateway-img/1.png)

确定一台锐捷网关

弱口令:

    user:guest
    passwd:guest

登录成功，但由于只是guset，权限有限，只能看到流控/行为/安全标签页,而且几乎什么都看不到。

## 0x02 提权

burpsuit抓取经过HTTP PROXY的POST数据包

![Alt text](https://github.com/Aquilao/Blog/raw/master/assets/img/Elevation_of_privilege-Ruijie_gateway-img/2.png)

分析得出：`command`后面就是执行的命令，POST数据包解析后执行的命令，此为`show clock`，但此`guest`受限制，不可执行所有的命令。

查阅了RUIJIE文档，发现`webmaster`可用于权限提升和降低！！

构造POST请求，用`webmaster level 0 username guest password guest`命令来提升 guest 的权限，同时把`strurl`中的`exec`改为`config`（试探）
![Alt text](https://github.com/Aquilao/Blog/raw/master/assets/img/Elevation_of_privilege-Ruijie_gateway-img/3.png)

成功了。
guest能看到完整的流控/行为/安全标签页了。

![Alt text](https://github.com/Aquilao/Blog/raw/master/assets/img/Elevation_of_privilege-Ruijie_gateway-img/4.png)

我们的`Level` 变成了**0**

![Alt text](https://github.com/Aquilao/Blog/raw/master/assets/img/Elevation_of_privilege-Ruijie_gateway-img/5.png)

但是其他的标签页还是看不到，所以我们尝试改一下cookies的`user`值

Firefox中用打开Firefox命令行并运行命令

    >> cookie set user = admin

然后我们刷新一下页面就以admin登录了，自此我们拿到了这台网关的最高权限

**2018.10.14 更新：发现新版的 Firefox 已经不能运行该命令了~，不过依然可以通过 F12 中的 Storage 标签页修改 Cookie，总之只要把 Cookie 中的 user 字段从 guest 改为 admin 就行了，方法很多。**

![Alt text](https://github.com/Aquilao/Blog/raw/master/assets/img/Elevation_of_privilege-Ruijie_gateway-img/6.png)

## 0x03 利用

在web管理界面我们可以随意的更改管理员的密码，而且可以增加和删除管理员

![Alt text](https://github.com/Aquilao/Blog/raw/master/assets/img/Elevation_of_privilege-Ruijie_gateway-img/7.png)

或者我们可以创建和删除VPN帐号。但其实我们在这里可以查看原有的VPN帐号，密码框是用html文本框的`password`属性，所以看起来只是几个小点，但我们可以使用Firebug修改`password`属性，所以这几个小点也变成明文了。

![Alt text](https://github.com/Aquilao/Blog/raw/master/assets/img/Elevation_of_privilege-Ruijie_gateway-img/8.jpg)

就这样，我们获取到了所有的VPN的帐号和密码～如果攻击者是在是外网的话直接就进入内网做进一步渗透了。

同时，POST请求也变成了一个WEB版本的`consloe`，可以远程执行任意命令。


## 0x04 修复

在意识到这个漏洞所带来的风险后，我们决定立即修复，禁用了`GUEST`和`MANAGER`俩个用户。

## 0x05 总结
我们JJUSEC全体成员，作为学校的一份子，希望提醒各位重视信息安全，这不仅是为了学校本身，也是为了各位领导和教职工以及学生的个人信息安全。
