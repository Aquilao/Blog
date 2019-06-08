---
layout: post
title: "如何使用 ICMPTX 消除身份验证代理"
date: 2018-1-22
excerpt: "消除身份验证代理"
translation: ture
tags:
- Wi-Fi hacking
comments: true
---

欢迎回来，我的新手 hacker 们！

在之前的Wi-Fi教程中，我已经向你展示了创建 Evil Twin，DoS 无线 AP 以及破解 WEP 和 WPA2 密码的方法，但在这篇教程中，我将会向你展示一些与众不同的东西。

在很多餐厅、旅馆、机场、飞机和咖啡馆中，他们都对无线 AP 进行了开放式身份验证，但是一旦连接到 AP ，你就会发送一个请求到我们要求你提供证书。在世界各地的许多商业机构中这是非常普遍的。要获得必要的证书，通常会收取相关费用。

如果你没有信用卡，或忘记了你的信用卡然而却需要访问互联网呢？如果服务器接受 ICMP （ping 的协议）并且你足够有耐心的话，你依然可以访问。

另外，想象一个场景，你需要偷偷检索一个文件，发送一条消息，或者检索一个消息，比如网络间谍或网络战争的情况，而几乎不留下你的活动的痕迹。这可能是你的可选择的一个方法。

注意：这是一个更先进的技术，所以如果你是刚开始接触 hacking ，那么在尝试这些之前，应该使用一些更基本的技术。

ICMP：网际报文控制协议

就像你知道的那样，ICMP 是一种用于检测活动主机的协议。我们可以通过简单地输入以下内容来确定主机是否处于活动状态（新手注意）：

	ping <IPaddress>



有多种类型的 ICMP 消息，但是这个是回应请求（类型0）和回应应答（类型8）。尽管几乎我们所有人都使用 ping，但请记住，还有其他类型的 ICMP 在扫描或 hacking 系统时可能会派上用场，这些系统可能会阻止接收或丢弃 ICMP 类型为0的数据包。

如果服务器接受ICMP（大多不会做安全防范措施），则可以使用ICMP绕过通过代理进行身份验证的需求（该网页要求你提供证书）。因为速度非常慢，所以我不建议将其用于日常使用，但是如果你不想购买服务，则可以通过一种非常创新的方法来获取你的 email，或者说你想访问网页却不被追踪。

### Step 1：打开 Kali & 下载 Icmptx

首先，让我们启动 [Kali Linux](https://null-byte.wonderhowto.com/how-to/hack-like-pro-getting-started-with-kali-your-new-hacking-system-0151631/) 并下载 icmptx。由于 icmptx 在 Kali 存储库中，所以我们只需要输入：

	apt-get install icmptx

![install icmptx](https://img.wonderhowto.com/img/85/51/63549223146189/0/hack-wi-fi-evading-authentication-proxy-using-icmptx.w1456.jpg)

这会安装 icmptx 到你的 Kali 操作系统里

### Step 2：获取帮助

接下来,让我们看看 icmptx 的帮助， 只需要简单的输入：

	icmptx

![icmptx_help](https://img.wonderhowto.com/img/57/14/63549223159783/0/hack-wi-fi-evading-authentication-proxy-using-icmptx.w1456.jpg)

这个帮助界面将会出现。正如你所看到的，语法非常直接且简单。不幸的是，实际用起来上不是这样的。

当我们下载 icmptx 时，它同时安装了一个手册页面，所以我们可以来查看它，输入：

	man icmptx

手册页并没有提供比帮助页面更多的信息。

### Step 3：服务器端代理

icmptx 的工作方式是在你的客户端和网络上的目标之间建立一个代理/服务器。首先，我们设置代理/服务器。

要设置代理/服务器，语法很简单：

	icmptx -s 10.0.0.1

![icmptx -s](https://img.wonderhowto.com/img/50/29/63549223187394/0/hack-wi-fi-evading-authentication-proxy-using-icmptx.w1456.jpg)

这表明在 IP 地址为10.0.0.1的服务器/代理。这只是一个例子，你将需要用你尝试连接的目标 IP 地址替换这个 IP。

### Step 4: 隧道

接下来，我们需要建立一个隧道。 隧道为基于用户的应用提供分组传输和接收的地方。 由于icmptx是一个基于用户的应用程序，我们需要建立一个隧道来发送和接收数据包，在这种情况下是ICMP数据包。

我们要检查我们的内核是否支持隧道，输入：

	ifconfig tun0

![ifconfig](https://img.wonderhowto.com/img/61/96/63549223201033/0/hack-wi-fi-evading-authentication-proxy-using-icmptx.w1456.jpg)


这个回显表明我们的 Debian 操作系统（Kali 基于 Debin）支持隧道。现在我们在服务器上建立一个隧道。

![ICMP tunnel](https://img.wonderhowto.com/img/04/26/63549223212643/0/hack-wi-fi-evading-authentication-proxy-using-icmptx.w1456.jpg)

### Step 5：IP 转发

接下来，我们需要先配置这个服务器，忽略 ICMP 请求，第二转发 IP 流量。如果我们没有告诉内核忽略 ICMP 请求，它会回应一个回应应答（类型8），这是正常的回应。我们不希望出现这种情况。我们希望的是 ICMP 流量进入服务器并通过它。

我们可以让内核忽略 ICMP 流量，输入：

	echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all

![icmptx -c](https://img.wonderhowto.com/img/89/65/63549223236611/0/hack-wi-fi-evading-authentication-proxy-using-icmptx.w1456.jpg)

然后我们也需要在这个系统上建立一个隧道。

![ICMP tunnel](https://img.wonderhowto.com/img/48/31/63549223272080/0/hack-wi-fi-evading-authentication-proxy-using-icmptx.w1456.jpg）

接下来，我们需要建立一个到代理的路由。

![add new route host](https://img.wonderhowto.com/img/36/99/63549223289877/0/hack-wi-fi-evading-authentication-proxy-using-icmptx.w1456.jpg)

最后，我们需要设置一条通过我们创建的隧道（tun0）到我们想要访问的 Web 服务器的路由。

![add new route gw](https://img.wonderhowto.com/img/24/53/63549223301174/0/hack-wi-fi-evading-authentication-proxy-using-icmptx.w1456.jpg)

现在，当你想访问 Web 上的站点，你可以这样做，没有认证，差不多可以说是完全隐匿！

虽然使用 icmptx 可能不是一个每天访问 Web 的好办法，但这是个在紧要关头或在严重的情况下秘密的切实可行的方法，它会让你通过网络认证，并留下几乎没有踪迹。很少有网络安全管理员会寻找ICMP流量来追踪你的活动，而且由于你不必认证，所以你的踪迹几乎是看不见的。另外，如果你在僵尸网络上设置了服务器，没有高超的取证技能只能把线索带回僵尸服务器上。

[原文](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-evading-authentication-proxy-using-icmptx-0150347/)
