---
layout: post
title: "用Cowpatty 破解WPA2-PSK密码"
date: 2017-12-17
excerpt: "破解WPA2"
translation: ture
tags:
- Wi-Fi hacking
comments: true
---

欢迎回来，我的新手黑客们！

作为我的[hacking Wi-Fi](https://null-byte.wonderhowto.com/how-to/wi-fi-hacking/)系列的一部分，我想演示另一个优秀的破解WPA2-PSK密码的黑客软件。 在我上一篇文章中，我们[使用aircrack-ng来破解WPA2](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-cracking-wpa2-psk-passwords-using-aircrack-ng-0148366/)。 在本教程中，我们将使用由无线安全研究员Joshua Wright开发的一个名为cowpatty（常被程式化为coWPAtty）的软件。 这个应用程序简化和加速对WPA2密码的字典/混合攻击，所以让我们开始吧！

### Step 1：找到Cowpatty

Cowpatty是包含在BackTrack软件套件中的数百个软件之一。出于某种原因，它并没有放在/pentest/wireless目录下，而是放在/usr/local/bin目录下，所以让我们在那里开始。

- cd /usr/local/bin

因为cowpatty位于/usr/local/bin目录下，而且这个目录应该在PATH中，所以我们应该可以从BackTrack的任何目录运行它。

### Step 2：找到Cowpatty的帮助界面

要简要地介绍一下cowpatty选项，你只需要输入：

- cowpatty

BackTrack将为你提供一个简短的帮助界面。注意，cowpatty需要以下所有内容。

- 一个字典
- 一个包含被捕获目标密码哈希的文件
- 目标AP的SSID

### Step 3：设置你的无线网卡为监听模式

就像[用Aircrack-Ng 破解WPA2-PSK密码](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-cracking-wpa2-psk-passwords-using-aircrack-ng-0148366/)中的一样，你需要把你的无线网卡设置为监听模式。

- airmon-ng start wlan0

### Step 4：开始捕获一个文件

接下来，我们需要启动来捕获一个文件，在捕获4次握手的时候将存储哈希密码。

- airodump-ng --bssid 00:25:9C:97:4F:48 -c 9 -w cowpatty mon0

这将在所选信道（-c 9）上的选定AP（00:25:9C:97:4F:48）上开始捕获，并将哈希保存在名为cowcrack的文件中。

### Step 5：捕获握手包

现在，等某人连接到这个AP，我们就会捕获到它的哈希，airodump-ng将会向我们展示被捕获的握手包以及连接者。

### Step 6：运行Cowpatty

现在，我们有了密码的哈希了，我们能用cowpatty和我们的字典来破解哈希

- cowpatty -f /pentest/passwords/wordlists/darkc0de.lst -r /root/cowcrack-01.cap -s Mandela2

就像你看到的一样，cowpatty正在生成我们的单词列表中的每个单词的哈希，SSID作为seed，并将其与捕获的散列进行比较。当哈希匹配时，它就会显示AP的密码。

### Step 7：生成你自己的哈希

尽管运行cowpatty能让这变得简单，但这也是很缓慢的。密码哈希用SHA1与SSID的哈希seed。这意味着不同SSID上的相同密码将生成不同的哈希值。这可以防止我们对所有AP使用彩虹表。Cowpatty必须接受你提供的密码列表，并计算每个密码的SSID。这是非常消耗CPU和缓慢的。

Cowpatty现在支持使用预先计算的散列文件而不是纯文本文件，使WPA2-PSK密码的破解速度提高了1000倍！预先计算的散列文件可从WiFi教会获得，并且这些预先计算的散列文件使用172,000个字典文件和1,000个最常用的SSID生成。因为这是有用的，如果你的SSID是不是在这1000个之内，这个散列表确实帮不到你。

在这种情况下，我们需要生成自己的哈希，为我们的目标SSID。我们可以通过使用一个程序称为genpmk。我们可以为SSID为“mandela2”生成哈希文件的“darkcode”字典，输入：

- genpmk -f /pentest/passwords/wordlists/darkc0de.lst -d hashes -s Mandela2

### Step 8：使用我们的哈希



一旦我们为特定的SSID产生了我们的哈希，然后我们可以通过cowpatty破解密码，输入： 

- cowpatty -d hashfile -r dumpfile -s ssid

[原文](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-cracking-wpa2-psk-passwords-with-cowpatty-0148423/)
