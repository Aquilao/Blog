---
layout: post
title: "Aircrack-Ng套件入门"
date: 2018-1-5
excerpt: "Aircrack-Ng suite"
translation: ture
tags: []
comments: true
---

欢迎回来，我的新手hacker们！

在我的[Wi-Fihacking](https://null-byte.wonderhowto.com/how-to/wi-fi-hacking/)系列的第一部分中，我们解释了与Wi-Fi相关的[基本术语和技术](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-getting-started-with-terms-technologies-0147659/)。现在你已经牢牢掌握了Wi-Fi究竟是什么以及它是如何工作的，我们可以开始深入探讨如何破解Wi-Fi的更进一部的主题。

在这篇文章中，我们将看一看这世界上最好的Wi-Fi hacking软件——aircrack-ng，我们之前用它[把烦人的邻居从你的网络中踢出去](https://null-byte.wonderhowto.com/how-to/hack-like-pro-get-even-with-your-annoying-neighbor-by-bumping-them-off-their-wifi-network-undetected-0147206/)。几乎所有后来的黑客都会使用aircrack-ng，所以我认为从一些基础知识入手以及如何使用这一切是明智的。

首先，aircrack-ng不是一个单独的工具，而是一组用来暗中操纵和破解Wi-Fi网络的工具套件。在这组套件中有个用来破解密码的工具叫做aircrack，但是在破解之前我们需要用其他工具进行其他几个步骤。此外，aircrack-ng还可以进行DOS攻击以及rogue AP，caffe latte，evil twin等等。

那么让我们开始从Aircrack-Ng套件入门入门吧！

### 注意

ng代表新一代，因为aircrack-ng取代了一个不再被支持的被叫做aircrack的旧套件。

### Step 1：iwconfig

在开始使用aircrcak-ng之前，我们需要确定BackTrack能够识别你的无线网卡。我们可以在任何Linux系统中输入：

	iwconfig

![iwconfig](https://img.wonderhowto.com/img/41/43/63509567806108/0/hack-wi-fi-getting-started-with-aircrack-ng-suite-wi-fi-hacking-tools.w1456.jpg)

我们可以看到BackTrack识别出了我的USB无线网卡，它告诉我它有802.11bgn的能力，ESSID是关闭的，模式为管理等。

### Step 2：Airmon-Ng

我们将会看到的第一个工具也是在几乎所有的Wi-Fi hack中都出现的airmon-ng，可以将我们的无线网卡转化到混杂模式。没错，这意味着我们的无线网卡将会连接到任何人！

嗯，这听起来不错。当我们的网卡处于混杂模式时，这意味着它可以看到和接收所有的网络流量。通常情况下，网卡只能接收预定给它们的数据包（由网卡的MAC地址决定），但是使用airmon-ng时，它将接收所有的无线流量。

我们可以通过输入airmon-ng，动作（start/stop），以及接口（mon0）：

	airmon-ng start wlan1

![airmon-ng](https://img.wonderhowto.com/img/04/08/63509567821178/0/hack-wi-fi-getting-started-with-aircrack-ng-suite-wi-fi-hacking-tools.w1456.jpg)

Airmon-ng通过我们的无线网卡的一些关键信息（包括芯片组和驱动程序）作出响应。最重要的是，注意它已经把我们的无线适配器的名称从wlan1更改为mon0。

### Step 3：Airodump-Ng

我们需要的下一个在aircrack-ng套件中的工具是airodump-ng，它能捕获我们规范的数据包。这在密码破解中特别有用。

我们可以通过输入airodump-ng命令和被重命名的混杂模式接口（mon0）来启动这个工具：

	airodump-ng mon0

![airmon-ng](https://img.wonderhowto.com/img/25/91/63509567831583/0/hack-wi-fi-getting-started-with-aircrack-ng-suite-wi-fi-hacking-tools.w1456.jpg)

正如我们在上面的屏幕截图中所看到的，airodump-ng显示所有的AP（接入点），其范围内的BSSID（MAC地址），功率，信标帧的数量，数据包的数量，信道，速度，加密方法，使用的密码类型，使用的认证方法，最后是ESSID。

对于我们hacking Wi-Fi的目的而言，最重要的部分是BSSID和信道。

### Step 4：Aircrack-Ng

Aircrack-ng是aircrack-ng套件的主要程序，用来破解密码。在捕获WPA握手帧之后，它能够使用统计技术和字典来破解WPA和WPA2的WEP。

### Step 5：Aireplay-Ng

Aireplay-ng是我们的Aircrack-ng军火库中的另一个强大工具，它可以用来生成或加速AP上的流量。这使的它在进行像deauth attack来踢掉AP上的所有用户、WEP和WPA2密码攻击以及ARP包注入和重放攻击的攻击尤其有用。

Aireplay-ng可以从两个来源获取数据包：

1.分组的实时数据流
2.预先捕获的pcap文件

pcap文件是与数据包捕获工具（如libpcap和winpcap）关联的标准文件类型。如果你曾经使用过Wireshark，那么你最有可能使用pcap文件。

![aireplay](https://img.wonderhowto.com/img/07/86/63509567842706/0/hack-wi-fi-getting-started-with-aircrack-ng-suite-wi-fi-hacking-tools.w1456.jpg)

我们可以在aireplay-ng帮助屏幕前半部分界面中看到，aireplay可以通过接入点的BSSID，源或目的地的MAC地址，最小和最大包长度等进行过滤。我们向下滚动帮助界面，我们可以看到一些使用aireplay-ng的攻击选项：

![aireplay](https://img.wonderhowto.com/img/49/53/63509567852206/0/hack-wi-fi-getting-started-with-aircrack-ng-suite-wi-fi-hacking-tools.w1456.jpg)

这些包括deauth，fake deauth，interactive，arpreplay（快速WEP破解所必需的），chopchop（用于WEP数据包解密而不破解密码的统计技术），fragment，caffe latte（攻击客户端）等等。

aircrack-ng套件中的这四个工具是我们的Wi-Fi hacking主力。几乎所有的Wi-Fi hack都会用到这些技术。我们的一些更具攻击性的工具包括airdecap-ng，airtun-ng，airolib-ng和airbase-ng。我们来简要介绍一下这些工具。

### Step 6：Airdecap-Ng

一旦我们破解了密钥，Airdecap-ng使我们能够解密无线流量。换句话说，一旦我们在AP上并拥有密钥，我们不仅可以使用接入点上的带宽，而且可以在无线接入点上解密每个人的流量，并观察他们正在做的一切（密钥用于访问和加密）。

### Step 7：Airtun-Ng

Airtun-ng是一个可以创建虚拟隧道接口的软件。我们可以使用airtun-ng设置无线流量的IDS，以检测无线接入点上的恶意或其他流量。因此，如果我们希望获得特定类型流量的警报（请参阅我的关于[打造类似PRISM的间谍工具的教程](https://null-byte.wonderhowto.com/how-to/hack-like-pro-create-your-own-prism-like-spy-tool-0147583/)），我们可以使用airtun-ng来设置连接到IDS的虚拟隧道，就像Snort来给我们发送警报。

### Step 8：Airolib-Ng

Airolib-ng可以存储或管理ESSID（接入点的名称）和密码列表，这将有助于加速WPA/WPA2密码破解。

### Step 9：Airbase-Ng

Airbase-ng使我们能够将笔记本电脑和无线网卡变成一个AP。这在进行rogue AP或evil twin攻击时特别有用。总的来说，airbase-ng允许我们攻击客户端，而不是AP，并促使客户与我们连接，而不是真正的AP。

这些是aircrack-ng套件中的主要工具，我们将在Wi-Fi hacking时使用。还有其他工具，但只有这些是我们重点介绍的。

在我们的下一个指南中，我们将开始探索无线安全技术，创建一个evil twin AP，所以一定要继续回来。如果你有任何问题，请在下面评论或在Null Byte论坛中开始讨论，我们会尽力帮助你。

[原文](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-getting-started-with-aircrack-ng-suite-wi-fi-hacking-tools-0147893/)
