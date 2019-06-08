---
layout: post
title: "用Aircrack-Ng 破解WPA2-PSK密码"
date: 2017-12-10
excerpt: "破解WPA2"
translation: ture
tags: []
comments: true
---

欢迎回来，我的新手黑客们！

当Wi-Fi在20世纪90年代第一次被开发出来时，有线等效保密被创造来为无线通信保密。WEP已经被证明有很大的缺陷，很容易被破解。你可以在我的[初学者指南](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-getting-started-with-terms-technologies-0147659/)中阅读更多关于hacking Wi-Fi的文章。

WPA2-PSK系统的弱点是加密的密码在所谓的四次握手中被共享。当客户端对接入点（AP）进行认证时，客户端和AP经过4个步骤来向AP认证用户。如果我们可以在那个时候拿到密码，我们就可以尝试破解它。

在我们[Wi-Fi Hacking](https://null-byte.wonderhowto.com/how-to/wi-fi-hacking/)系列教程的这篇文章中，我们将看看使用[aircrack-ng](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-getting-started-with-aircrack-ng-suite-wi-fi-hacking-tools-0147893/)和对加密密码的[字典攻击](https://null-byte.wonderhowto.com/search/dictionary-attack/)，然后在4次握手中抓取密码。如果你正在寻找一个更快的方法，我建议你也看看我的文章，关于[使用coWPAtty破解WPA2-PSK密码](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-cracking-wpa2-psk-passwords-with-cowpatty-0148423/)。

### Step 1：设置你的无线网卡为监听模式

让我们开始将我们的无线适配器置于设为监听模式。

这与将有线网卡设为混杂模式相似。它使我们能够看到所有广播传递到我们的无线网卡的数据包。打开一个终端，输入：

- airmon-ng start wlan0

注意你的wlan0网卡被airmon-ng重命名为mon0

### Step 2:使用Airodump-Ng捕获流量

现在，我们的无线适配器处于监听模式，我们可以看到所有的的无线流量。我们可以通过简单的使用airodump-ng命令来获取流量。

这个命令抓取无线网卡可以看到的所有流量，并显示关键信息，包括BSSID（AP的MAC地址），功率，信标帧数，数据帧数，信道，速度，加密（if 有的话），最后是ESSID（我们大多数人称之为SSID）。让我们输入以下内容：

- airodump-ng mon0

注意，所有可见的接入点都列在屏幕的上半部分，客户端列在屏幕的下半部分。

### Step 3:让Airodump-Ng监听一个信道

我们的下一步就是把注意力放在一个AP的一个信道上，并从中捕获关键数据。我们需要BSSID和频道来做到这一点。我们打开另一个终端并输入：

- airodump-ng --bssid 08:86:30:74:22:76 -c 6 --write WPAcrack mon0

- 08:86:30:74:22:76 是AP的BSSID
- -c6 是AP的信道
- WPAcrack 是你想写入的文件
- mon0 是监听模式的无线网卡名

正如你看到的一样，我们现在关注的是从第6信道的Belkin276的ESSID捕获一个AP的数据.Belkin276可能是默认的SSID，这是无线hacking的主要目标，因为离开默认的ESSID通常不会花费太多的精力来保护他们的AP。

### Step 4: Aireplay-Ng洪水攻击

为了捕获加密的密码，我们需要客户端对AP进行身份验证。如果他们已经通过身份验证，我们可以取消身份验证（启动他们），他们的系统将自动重新进行身份验证，从而我们可以在此过程中获取加密的密码。我们打开另一个终端并键入：

- aireplay-ng --deauth 100 -a 08:86:30:74:22:76 mon0

- 100 是你要发送的取消身份验证帧的数量
- 08:86:30:74:22:76 是AP的BSSID
- mon0 是监听模式的无线网卡名

### Step 5: 抓取握手包

在之前的步骤中，我们将用户从自己链接的AP中断开，现在，当他们重新进行身份验证时，airodump-ng将尝试在新的4次握手中获取密码。让我们回到我们的airodump-ng终端，看看我们是否成功。

注意，airodump-ng说“WPA握手”。这是它告诉我们我们成功地抓取加密密码的方式！这是成功的第一步！

### Step 6: 让我们用Aircrack-Ng破解密码！

既然我们已经在我们的文件“WPAcrack”中写入了加密的密码，那么我们可以使用我们选择的密码字典来运行aircrack-ng来破解该文件。请记住，这种类型的攻击只和密码文件一样好。我将使用名为darkcOde的BackTrack中的aircrack-ng附带的默认密码字典。

我们现在尝试打开另一个终端并输入以下内容来破解密码：

- aircrack-ng WPAcrack-01.cap -w /pentest/passwords/wordlists/darkc0de

- WPAcrack-01.cap 是我们在airodump-ng命令中写入的文件的名称
- /pentest/passwords/wordlist/darkc0de 是你的密码字典的绝对路径

### 这需要多长时间？

这个过程可能相对缓慢和乏味。根据你的密码字典的长度，可能会等待几分钟到几天。在我的双核2.8G的英特尔处理器上，它能够测试每秒500多个密码。这可以达到每小时约180万个密码。你的需要的时间可能会有所不同。

当找到密码时，它会出现在你的屏幕上。 请记住，密码文件是至关重要的。 首先尝试使用默认密码字典，如果不成功，则进入更大，更完整的密码字典，如其中的一个。

- [crackstation的密码字典](https://crackstation.net/buy-crackstation-wordlist-password-cracking-dictionary.htm)
- [skullsecurity的密码字典](https://www.skullsecurity.org/wiki/index.php/Passwords)

[原文](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-cracking-wpa2-psk-passwords-using-aircrack-ng-0148366/)
