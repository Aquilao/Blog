---
layout: post
title: "用Bully破解WPS PIN来获取Wifi密码"
date: 2017-11-26
excerpt: "破解WPS"
translation: ture
tags:
- Wi-Fi hacking
comments: true
---


用Bully破解WPS PIN来获取Wifi密码

欢迎回来，我的新手黑客们！

像生活中的其他东西一样，有很多方法完成Hack。举个例子，好的黑客们经常有很多方法来骇入一个系统。如果他们不这么做的话，那么他们不可能总是成功。没有一种Hack方法无论何时都可以在所有的系统上生效的。

我有列出[很多hack WI-FI的方法](https://null-byte.wonderhowto.com/how-to/wi-fi-hacking/)在Null Byte上，包括破解[WEP](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-cracking-wep-passwords-with-aircrack-ng-0147340/)和[WPA2](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-cracking-wpa2-psk-passwords-using-aircrack-ng-0148366/)密码和创建[Evil Twin](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-creating-evil-twin-wireless-access-point-eavesdrop-data-0147919/)和[Rogue AP](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-creating-invisible-rogue-access-point-siphon-off-data-undetected-0148031/)

几年前，Alex Long演示了如何使用Reaver在运行了WPS的旧固件和上破解WPS PIN。最近，一种新的WPS hacking 工具已经出现在市面上，并被收入在我们的[Kali](https://null-byte.wonderhowto.com/how-to/hack-like-pro-getting-started-with-kali-your-new-hacking-system-0151631/)中。这就是Bully。

### 为什么WPS 这么脆弱

WPS代表Wi-Fi Protected Setup（Wi-Fi保护设置），旨在为普通的家庭所有着设置更简单更安全的AP。在2006年首次推出，到2011年，它被发现有一个严重的设计缺陷。WPS PIN码可能被暴力破解轻易地搞定。

在PIN中只有7个未知数，有999999个可能性，大多数系统可以在几个小时内尝试非常多的组合。一旦发现WPS PIN，用户就可以使用这个PIN找到WPA2预共享密钥（密码）。由于对受WPA2保护的AP的暴力破解可能需要几个小时到几天的时间，如果在AP上启用了此功能且未升级，那么获取PSK的速度可能更快。

### 成功的关键

但是，重要的是得注意，新的AP不再有这个漏洞。 这类攻击只会对2006年和2012年初期间出售的受害者造成影响。由于许多家庭保留了这些AP很多年，所以说我们身边还有不少是其中之一。

（译者注：省略了一段无线网卡和树莓派的广告～）

### Step 1：启动Kali

让我们开始启动我们最喜欢的Hacking Linux 发行版，Kali。然后打开一个终端

为了确定我们的一些无线连接及其名称，我们可以输入：

	iwconfig

正如我们所看到的，这个系统有一个名为wlan0的无线连接。你的可能会有所不同，所以一定要检查。

### Step 2：设置你的无线网卡为监听模式

下一步是将你的无线网卡置于监听模式。这与有线连接上的混杂模式类似。换句话说，它使我们能够看到所有广播传递到我们的无线网卡的数据包。我们可以使用Aircrack-ng套件中的一个工具Airmon-ng来完成这个任务。

	 airmon-ng start wlan0

接下来，我们需要使用Airodump-ng来查看我们周围的无线AP的信息。

	airodump-ng mon0

就像你所看到的，我们可以看到几个AP。我感兴趣的第一个"Mandela2"(Wi-Fi名）我们将需要它的BSSID（MAC地址）、信道，和SSID，以便能够破解其WPS PIN。

### Step 3：使用Airodump-Ng获取必要的信息

最后，我们需要做的就是把这些信息填入我们的Bully命令中。

	bully mon0 -b 00:25:9C:97:4F:48 -e Mandela2 -c 9

让我们分解这个命令，看看发生了什么。

- mon0 监听模式下无线网卡名
- --b 00:25:9C:97:4F:48 目标AP的BSSID
- -e Mandela2 AP SSID
- -c 9 是P正在广播的信道

所有这些信息都可以在Airodump-ng的界面上找到。

### Step 4：打开 Bully

当我们按下回车键，Bully将开始尝试破解WPS PIN。

如果这个AP很容易受到这种攻击，Bully会在3到5个小时内破解出WPS PIN和AP密码。

[原文](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-breaking-wps-pin-get-password-with-bully-0158819/)
