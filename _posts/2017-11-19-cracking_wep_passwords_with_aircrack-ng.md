---
layout: post
title: "用Aircrack-ng破解WEP密码"
date: 2017-11-19
excerpt: "破解WEP"
translation: ture
tags:
- Wi-Fi hacking
comments: true
---



欢迎回来，我的新手黑客们！

当Wi-Fi在九十年代后期首次被开发和普及时，安全性并不是一个主要的问题。与有线连接不同的是，任何人都可以简单地连接到Wi-Fi接入点（AP）并窃取带宽，或者更糟——嗅探流量。

保护这些接入点的第一次尝试被称为有线等效保密（Wired Equivalent Privacy），或简称为WEP。这种加密方法已经存在了相当长的一段时间，并被发现了一些弱点。它大体上已经被WPA和WPA2取代。

尽管存在这些已知的弱点，但仍有相当数量的这些传统AP正在使用中。我最近（2013年7月）在Northern Virginia的一个主要的美国国防部承包商工作，在那个大楼里，可能有四分之一的无线接入点仍在使用WEP！

显然，一些家庭用户和小型企业几年前购买了他们的AP，并且从未升级过，没有意识到或者说不关心其安全性问题。

WEP中的缺陷使其容易受到各种统计破解技术的影响。WEP使用RC4进行加密，RC4要求初始化矢量（IVs）是随机的。在WEP中RC4的实施重复了每隔约6000帧IV。如果我们可以抓取到足够的IV，我们可以破译密钥！（译者注：即利用其IV不变性）

现在，你可能会问自己：“为什么我想要在拥有自己的Wi-Fi路由器和AP的时候[Hack Wi-Fi](https://null-byte.wonderhowto.com/how-to/wi-fi-hacking/)？”答案可以有很多。

首先，如果你Hack别人的Wi-Fi路由器，你可以在网络上匿名，或者更准确地说，用别人的IP地址。其次，一旦你Hack了Wi-Fi路由器，你可以解密他们的流量，并使用[Wireshark](https://null-byte.wonderhowto.com/how-to/spy-your-buddys-network-traffic-intro-wireshark-and-osi-model-0133807/)或[tcpdump](http://www.tcpdump.org/)等嗅探工具来捕获和监视所有流量。第三，如果你使用种子下载大文件，你可以使用别人的带宽，而不是你自己的。

让我们来看看如何破解WEP使用最好的无线黑客工具——aircrack-ng！Hacking无线是我个人的最爱！

## Step 1：在BackTrack中打开Aircrack-Ng

让我们开始启动BackTrack，并确保我们的无线网卡已被识别和运行。

	iwconfig

（译者注：原文每条命令下都有张相应的图片，我嫌麻烦就没放过来...）
我们注意到，我们的无线网卡被BackTrack识别，并被重命名为wlan0。你的可能是wlan1或wlan2。

## Step 2：将无线网卡设为监听模式

接下来，我们需要将无线网卡设为监听模式。我们可以输入：

	airmon-ng start wlan0

注意那块网卡的名字已经被airmon-ng改为mon0

## Step 3：开始捕获流量

我们现在需要开始捕获流量。我们通过使用监听模式网卡mon0的airmon-ng命令来完成此操作。

	airodump-ng mon0

就像我们看到的那样，我们现在能够看到我们范围内的所有AP和客户机！

## Step 4：对一个AP进行专门的流量捕获

就像你在截屏中看到的那样，有几个WEP加密的AP。让我们用“wonderhowto”的ESSID做目标。让我们复制这个AP的BSSID，并开始在该AP上捕获。

	airodump-ng --bssid 00:09:5B:6F:64:1E -c 11 -w WEPcrack mon0

这将开始捕获信道11上SSID为“wonderhowto”的数据包，并将它们写入文件WEPcrack中，格式为pcap。如果我们非常有耐心的话，这个命令现在将允许我们捕获数据包以破解WEP密钥。

但是我们并没有什么耐心，我们现在就要！我们要尽快破解这个密钥，因此，我们需要将数据包注入到AP中。

我们现在需要等待有人连接到AP，以便我们可以从他们的网卡获取MAC地址。当我们有他们的MAC地址时，我们可以修改自己的MAC并将数据包注入他们的AP。正如我们在屏幕截图底部看到的，有人连接到“wonderhowto”AP。现在我们可以加快攻击速度了！

## Step 5:注入ARP流量

为了欺骗他们的MAC并注入数据包，我们可以使用aireplay-ng命令。我们需要AP的BSSID和连接到AP的客户端的MAC地址。我们将捕获一个ARP数据包，然后重播这个ARP数千次，以生成我们需要破解WEP的IV。

	aireplay-ng -3 -b 00::09:58:6F:64:1E -h 44:60:57:c8:58:A0 mon0

现在当我们注入ARP数据包注入AP时，我们将捕获在我们的airodump文件WEPcrack中生成的IV。

## Step 6：破解密码

一旦在我们的WEPcrack文件中有几千个IV，我们需要做的就是运行aircrack-ng破解这个文件，比如这个：

	aircrack-ng WEPcrack-01.cap

如果我们有足够的IV，aircrack-ng将会在我们的屏幕里显示出密钥，通常以十六进制格式。只需拿着这个十六进制密钥，并在登录到远程AP时应用它，你就会有免费的无线！

[原文](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-cracking-wep-passwords-with-aircrack-ng-0147340/)
