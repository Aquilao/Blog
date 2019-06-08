---
layout: post
title: "选择一个好的WiFi Hacking策略"
date: 2017-11-12
excerpt: "如何选择一个好的WiFi Hacking策略"
translation: ture
tags:
- Wi-Fi hacking
comments: true
---


欢迎回来，我的新手黑客们！

由于很多读者来到Null Byte学习[how to hack Wi-Fi networks](https://null-byte.wonderhowto.com/how-to/wi-fi-hacking/)（这是在Null Byte最受欢迎的Hacking 区域）所以我觉得我应该写一篇“how-to（一个板块名）”关于选择一个好的WiFi Hacking策略。

很多新手来这里寻找Hack Wi-Fi的方法，但是不知道从哪里、该如何下手。并非所有的Hack都能在每种环境下起作用，所以选择正确的策略更有可能导致成功，减少浪费时间和挫败感。

在这里，在这里，我将通过最简单，最有效的第一步，直到最复杂，最困难的最后一步来制定策略。 一般来说，这个流程会有不错的成功率。

## 在你开始破解Wi-Fi密码之前

我强烈建议你阅读这篇文章，以熟悉无线Hacking的术语和基本技术。另外，要使用Wi-Fi破解工具Aircrack-ng，它确实能够有效的破解Wi-Fi密码，你需要安装Aircrack-ng兼容无线网卡。

（译者注：下面是一段看起来是无线网卡广告的介绍，我给省略了～）

## 破解WEP

WEP或着叫Wireless Equivalent Privacy（无线等效保密）是第一个被开发的无线加密技术。它很快被发现有缺陷，容易被攻破。尽管你不会找到任何新的WEP加密的AP，但仍有许多WEP AP仍在使用中。（在最近与美国国防部一个主要承包商进行的一次咨询演出中，我发现他们接近25％的AP使用WEP，所以它仍然还在那里。）

WEP很容易被Aircrack-ng使用统计破解方法破解。这几乎是肯定成功的（不要证明我错了）。 如果你能收集足够的数据包（这是关键），这是一个简单的过程。这是你需要Aircrack-ng兼容无线网卡的原因之一。你必须能够同时注入数据包来捕获数据包。大多数现成的无线网卡都无能为力。

- 不要错过：[用Aircrack-ng破解WEP密码](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-cracking-wep-passwords-with-aircrack-ng-0147340/)

要知道AP是否使用WEP，只需将鼠标悬停在AP上，即可显示其加密算法。请注意，这种方法只适用于AP使用WEP。它不适用于无线上的任何其他加密方案。如果你足够幸运找到一个WEP的无线接入点，你有希望可以在10分钟内破解密码，虽然有人声称在不到3分钟的时间内完成了这个任务。

## 破解WPS

许多Wi-Fi AP都配备了Wi-Fi保护设置（WPS），使普通家庭用户更容易在没有Wi-Fi安全措施的知识的情况下建立他们的无线AP。对我们来说幸运的是，如果我们可以破解WPS PIN码，我们就可以访问无线AP的控制面板。

这个PIN码比较简单，只有8个数字，其中一个是校验和，只剩下7个数字或10,000,000个可能性。单个CPU通常可以在几天内计算完这些可能性。虽然这可能看起来很慢，但多次暴力破解PSK（预共享密钥）的可能性可能需要更长的时间。

- 不要错过：[用Reaver攻破WPS PIN来获取密码](https://null-byte.wonderhowto.com/how-to/hack-wpa-wifi-passwords-by-cracking-wps-pin-0132542/)
- 不要错过：[用Bully攻破WPS PIN来获取密码](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-breaking-wps-pin-get-password-with-bully-0158819/)

如果无线AP启用了WPS，这是使用WPA2破解现代无线AP的首选方法。你可以使用Reaver或Bully与Aircrack-ng一起攻破这些WPS PIN。

## 破解WPA2

在WEP被攻破之后，无线行业开发了一种新的无线安全标准，即WPA2或Wi-Fi Protected Access II。几乎每个新的无线AP都已经内置了这个标准。虽然更难以破解，但并非不可能。

当客户端连接到AP时，有一个四次握手（4-way handshake），其中预共享密钥（PSK）从客户端机器传输到AP。我们可以捕获该PSK的哈希，然后使用字典或暴力破解。这可能会耗费很多时间，且并不总是成功。成功取决于你使用的字典和足够的时间。

- 不要错过：[用Aircrack-ng破解WPA2-PSK密码](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-cracking-wpa2-psk-passwords-using-aircrack-ng-0148366/)
- 不要错过：[用Cowpatty破解WPA2-PSK密码](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-cracking-wpa2-psk-passwords-with-cowpatty-0148423/)

一旦获得了捕获的PSK哈希，就不需要连接到AP。只要有足够的资源，你可以暴力破解任何PSK。

## Evil Twin（译者注:伪AP钓鱼）

如果我们不能在AP上破解密码，另一个可以成功的策略就是创建一个与已知AP完全相同的SSID但是由我们控制的Evil Twin —— 一个 AP。 关键是让目标连接到我们的AP，而不是真正的AP。

通常情况下，电脑会自动连接到信号最强的AP，所以打开AP的电源可能是这个黑客攻击的关键因素。当用户连接到我们的AP时，我们可以捕获并查看所有的流量，并捕获其他系统提供的任何其他证书。

- 不要错过：[如何创建一个Evil Twin AP来监听他人数据](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-creating-evil-twin-wireless-access-point-eavesdrop-data-0147919/)

Evil Twin的一个有效的变种是建立一个具有相同SSID的系统，然后向用户提供一个登录界面。许多公司办公室，旅馆，咖啡店等都使用这种类型的安全措施。当用户在我们的假登录界面上显示他们的凭据时，我们捕获证书并将其存储。然后，我们可以在真实的AP上使用这些证书来获得访问权限。

这个过程已经被称为Airsnarf的脚本自动化了。 不幸的是，Airsnarf已经过时了，但是我一直在努力更新它，并且很快就会提供脚本和教程。

## ICMPTX

如果一切都失败了，你绝对必须有互联网接入，ICMPTX通常在需要通过代理认证的无线网络上工作。这些包括一些学校和大学，酒店，咖啡店，图书馆，餐馆和其他公共Wi-Fi AP。它依赖于ICMP（ping所使用的协议）通常在AP上启用并传递到预期的IP地址或域。由于它不是TCP，它不涉及代理，只是穿过。

- 不要错过:[如何使用ICMPTX消除身份验证代理](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-evading-authentication-proxy-using-icmptx-0150347/)

这种攻击非常复杂和耗时，不适合新手进行Hacking。由于ICMP只能在每个数据包中携带少量的数据，所以速度很慢，但是在实际上必须有Internet访问且数据量较小的情况下（例如电子邮件），这种方法效果很好。

## 其他策略

这里有包括社会工程和许多Metasploit漏洞的目标系统有许多策略。当你接入目标系统时，你可以简单地从目标系统提取无线密码，方法是：

	C::\ProgramData\Microsoft\Wlansvc\Profiles\Interface\{Interface GUID}

这里，你会找到一个包含无线密码的十六进制编码的XML文件

获取无线AP访问权可以像破解WEP密钥一样简单，也可以像使用ICMPTX一样复杂，但无线访问可能被破坏。如果一切都失败了，则将网络中的一台机器作为目标，拥有它，然后如上所述获取密码。

[原文](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-selecting-good-wi-fi-hacking-strategy-0162526/)
