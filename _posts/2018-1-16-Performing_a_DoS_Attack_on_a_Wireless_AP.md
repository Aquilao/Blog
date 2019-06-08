---
layout: post
title: "在AP上进行拒绝服务(DoS)攻击"
date: 2018-1-16
excerpt: "无线 DoS"
translation: ture
tags:
- Wi-Fi hacking
comments: true
---

欢迎回来，我的新手黑客！

作为我的 Wi-Fi hacking 的一部分，我希望接下来看看拒绝服务（DoS)攻击，已经如何对一个无线 AP 进行 DoS 攻击。有很多方法可以做到这一点，但在本教程中我们将使用 aircrack-ng 的 aireplay 向 AP 反复发送解除认证帧。记住，hacking 无线网络不只是破解 Wi-Fi 密码。

### 我们的问题场景

让我们想象一个场景，你最好哥们的女朋友刚刚和他分手。他疯狂地爱着她，当然现在这使得他的心被蹂躏。他非常沮丧，举箸不食、夜寝不寐，更不用说为接下来的考试做准备。他甚至考虑过自杀。你忠于你最好的伙伴，帮助他度过一生中最糟糕的时光，并且在你的关心和体贴下，他重新振作起来了。

不幸的是，他有一场大学考试在明天。如果他还有几天的话，那他一定能通过那场考试。我们的任务就是对无线 AP 进行 DoS 攻击，使考试不能在原先安排的时间进行，学校会重新安排考试。这样就为我们最好的哥们提供了复习和恢复的时间。

### 我们的情景解决方案

所以，在考试当天，我们的哥们如期去教室，尽管完全没有准备通过考试。我们只需要让自己在足够接近无线 AP 的地方。这可能在走廊，隔壁房间或图书馆。大多数 AP 将延伸到300英尺（约100米），所以我们不必离得非常近。

另外，如果我们在我们的阿尔法无线网卡上放置一个高增益天线，我们可以离得更远。然后，我们掏出出我们运行了 BackTrack 并外接我们的阿尔法无线网卡的可靠电脑，来把我们的朋友从考试挂科中拯救出来！

### Step 1：打开一个终端

现在，我们已经在考试的无线 AP 范围内，让我们启动 BackTrack 并打开一个终端。让我们确定我们的无线网卡是否被 BackTrack 识别并运作。

- iwconfig

![iwconfig](https://img.wonderhowto.com/img/84/28/63510173874809/0/hack-wi-fi-performing-denial-service-dos-attack-wireless-access-point.w1456.jpg)

### Step 2：将你的无线网卡设为监听模式

我们的下一步就是把我们的无线网卡用 [airmon-ng](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-getting-started-with-aircrack-ng-suite-wi-fi-hacking-tools-0147893/) 设置为监听模式。

- airmon-ng start wlan0

![airmon](https://img.wonderhowto.com/img/46/99/63510173890269/0/hack-wi-fi-performing-denial-service-dos-attack-wireless-access-point.w1456.jpg)

就像我们看到的那样，Concord 大学是显示的第三个 AP。注意他的 BSSID（这是基于它的 MAC 地址的全局唯一标识符）并复制它。

### Step 4：连接到无线接入点

现在，我们需要让我们的电脑连上 AP。

![airodump-ng](https://img.wonderhowto.com/img/90/99/63510173913029/0/hack-wi-fi-performing-denial-service-dos-attack-wireless-access-point.w1456.jpg)

我们可以看到所有的连接在屏幕底部。我们可以在最左边的底部看到 AP 的 BSSID 和我们客户机的 MAC 地址。我们需要这些信息来进行这次 hack 的下一步。

### Step 5：在AP上广播取消身份验证用户

现在我们准备对AP上的所有用户进行认证（踢下线～）。我们需要发送数千个取消认证的帧，以防止任何人重新连接到 AP。我们可以在其他终端中输入：

- aireplay-ng --deauth 1000 -a 00:09:5B:6F:64:1E -h 44:6D:57:C8:58:A0 mon0

![aireplay-ng](https://img.wonderhowto.com/img/52/79/63510173923263/0/hack-wi-fi-performing-denial-service-dos-attack-wireless-access-point.w1456.jpg)

- 00:09:5B:6F:64:1E	  是 AP 的 BSSID
- 44:6D:57:C8:58:A0   是我们电脑的 MAC 地址
- 1000    是发送的取消认证帧的数量

当学生尝试连接到 AP 参加考试时，他们将无法连接，或者一旦他们连接，他们将被踢下线。教师或教授不太可能知道发生了什么事情，对于这个问题，学校的IT主管也不会（译者注：忽略这一句，否则可能会被一眼发现然后抓进保卫科～）。

### Step 6：成功！

我们需要保持这些解除认证的框架一直运行，直到老师或教授最后放弃并重新安排考试。

现在，我们最好的哥们已经有几天时间了，直到重新安排的考试再来一次。幸亏 BackTrack 和一些 hacking 技巧，我们把我们的好哥们从考试挂科中拯救出来了！

[原文](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-performing-denial-service-dos-attack-wireless-access-point-0147988/)
