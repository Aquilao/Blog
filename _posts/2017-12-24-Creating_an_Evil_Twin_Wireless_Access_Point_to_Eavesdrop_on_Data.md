---
layout: post
title: "创建一个Evil Twin AP来监听他人数据"
date: 2017-12-24
excerpt: "伪AP钓鱼"
translation: ture
tags: []
comments: true
---

欢迎回来，我的新手Hack们！

现在，我们熟悉了[技术、术语](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-getting-started-with-terms-technologies-0147659/)和[aircrack-ng套件](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-getting-started-with-aircrack-ng-suite-wi-fi-hacking-tools-0147893/)，我们最后终于可以开始hacking Wi-Fi。

我们的首要任务将会是创建一个evil twin AP。很多新手黑客急躁地想[破解Wi-Fi密码](https://mods-n-hacks.gadgethacks.com/how-to/crack-wi-fi-passwords-for-beginners-0139793/)来获得一些免费的带宽（不用担心，我们会的）,但是还有很多其他更强的的Wi-Fi hack，他们可不只是对带宽有威胁。

### 什么是Evil Twin AP？

Evil Twin AP是一个接入点，它看起来还有行为就像一个合法的AP一样，并诱使最终用户连接到我们的接入点。

我们的aircrack-ng套件有一个工具，airbase-ng，可以用来把我们的无线网卡转换成接入点。这是一个强大的hack工具，可以让我们能够看到来自客户端的所有流量，并进行中间人攻击。

### 我们将怎么做

在这个例子中，我们是一个私家侦探。我们已经被客户要求调查邻居是否下载和出售儿童色情的可能性。他们要求我们调查并确定他是否确实是，如果是的话，要收集证据。

### Step 1:开启 Airmon-Ng

首先，我们需要检查你的无线网卡是否在运行。

- iwconfig

正如我们所看到的，我们的无线网卡是运行，且已经分配名字为wlan0。我们的下一步是将我们的无线网卡置于监听或混杂模式。我们可以简单地输入：

- airmon-ng start wlan0

Airmon-ng将我们的无线设备置于监听模式，并将其更名为mon0。现在我们的无线网卡能够看到所有的无线流量。

### Step 2: 开启 Airdump-Ng

我们下一步就是开始捕捉无线网卡上的流量。我们通过输入：

- airodump-ng mon0

我们可以看到我们范围内的所有无线AP以及所有重要的统计数据。我们怀疑下载和销售儿童色情的邻居在SSID为“Elroy”的AP上。

如果我们的操作都对了，我们可以克隆他的AP，并让他连接到我们的evil twin。当他这样做的时候，我们将能够看到他的所有流量，并可能将我们自己的数据包/信息/代码插入到他的计算机中。

### Step 3:等待嫌疑人链接

现在，我们只需要等嫌疑人连接到这个无线AP。当他这样做时，它会出现在airodump-ng界面的下部分。

### Step 4:创建一个有相同SSID和MAC地址的AP

一旦他连接到他的AP，我们可以使用airbase-ng来创建一个evil twin AP。我们可以通过打开一个新的终端并输入：

- airbase-ng -a 00:09:5B:6F:64:1E --essid "Elroy" -c 11 mon0

00：09：5B：6F：64：1E是BSSID，Elroy是SSID，-c 11是嫌疑人AP的信道。

### Step 5: 取消身份验证或使他强制掉线

我们的下一步就是将“邻居”从他的接入点上掉线。802.11标准有一个称为解除认证（deauthentication）的特殊框架，正如你所期望的那样，对接入点上的每个人进行独立认证。当他的电脑尝试重新认证时，他会自动重新连接到ESSID为“Elroy”且信号最强的AP。

我们可以通过使用aireplay-ng的与deauth包做到这个：

- aireplay-ng --deauth 0 -a 00:09:5B:6F:1E

注意，我们再次在aireplay-ng命令中使用了他的BSSID。如果我们的信号比自己的AP强，他会自动重新连接到我们的evil twin！

### Step 6:开启电源！

evil twin的关键环节是确保我们的假冒的AP比原来的或者真实的AP更接近或者说信号更强。当物理访问不可用时，这可能是一个关键的弱点。在机场和其他公共场所，这是没有问题的，但是在我们这里，我们没有实际的接入，他的接入点很可能比我们的接近和且信号。不要让这阻碍我们！

首先，我们可以提高我们的AP信号，试图比他的更强。即使在隔壁，这也可以实现，因为大多数接入点自动将其功率降低到维持与客户的连接所需的最低限度。我们可以提高我们的AP的最大功率，输入：

- iwconfig wlan0 txpower 27

这个命令将把我们的输出功率提高到美国法定允许的最大值，即27 dBm或500 milliwatts。

在某些情况下，甚至提升到500毫瓦的功率被证明可能是不够的。如果我们尝试在我们的Alfa无线网卡上将功率提高到最大（1,000 mWs或30 dBm），我们会得到下面的错误信息（一些较新的卡实际上可以以2,000 mWs传输，或者是在美国法律上允许的四倍）。

- iwconfig wlan0 txpower 30

注意：下一步在美国是非法的，所以要小心使用它，除非你有特别的许可或本身就是执法人员。

每个国家都有自己的Wi-Fi规定。有些国家比美国拥有更强的信号和更多的信道。例如，玻利维亚允许使用12号信道和1000 mWs功率。只需输入以下内容，我们就可以让我们的Alfa卡使用玻利维亚法规：

- iw reg set BO

现在我们正处于玻利维亚监管领域了，我们可以通过输入以下内容来最大限度地增强我们的信号：

- iwconfig wlan0 txpower 30

检查输出功率,输入:

- iwconfig

而现在我们可以在第二行结束时看到，我们的功率现在可以达到30 dBm或1000 milliwatts，足以压倒所有其他本地接入点，甚至在几个房子之外的！

### 这台Evil Twin现在正在工作

现在，我们已经使我们的邻居连接到了我们的AP上，我们可以采取下一步措施来检测他的活动。

我们可以使用[Ettercap](https://null-byte.wonderhowto.com/how-to/spy-web-traffic-for-any-computers-your-network-intro-arp-poisoning-0131785/)这样的软件进行中间人攻击。这样，我们可以拦截，分析，甚至注入流量给这个用户。换句话说，因为他已经连接到我们的AP，我们几乎可以访问他的数据。如果他真的是下载或出售儿童色情，我们可以截获到它。

我们也应该能够拦截电子邮件和其他应用程序和网络的密码。我们甚至可以将一个[meterpreter](https://null-byte.wonderhowto.com/how-to/hack-like-pro-hacking-samba-ubuntu-and-installing-meterpreter-0135162/)或[其他监听器](https://null-byte.wonderhowto.com/how-to/listeners/)注入到他的系统中，以便进一步访问和控制。

[原文](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-creating-evil-twin-wireless-access-point-eavesdrop-data-0147919/)
