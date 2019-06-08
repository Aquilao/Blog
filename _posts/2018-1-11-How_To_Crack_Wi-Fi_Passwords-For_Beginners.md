---
layout: post
title: "如何破解 Wi-Fi 密码 - 给初学者！"
date: 2018-1-11
excerpt: "在 windows 平台上用 CommView 破解 WEP"
translation: ture
tags: []
comments: true
---

互联网连接已成为我们现代生活中的基本必需品。无线热点（俗称 Wi-Fi ）随处可见！

如果你有一台带有无线网卡的电脑，那么肯定会看到有很多网络在你身边。不幸的是，这些网络中的大多数都被密钥保护着。

你有没有想过使用这些网络的其中一个？当你搬到你的新房子的时候，你一定非常想检查你的邮件。在你的生活中最难的时候就是是当你的网络连接断掉的时后。

破解这些 Wi-Fi 密码是如何让你临时访问互联网的答案。这是一个全面的指导，可以教会初学者的如何轻易的破解WEP加密网络。

如果是你需要破解的是 WPA2-PSK 密码，你可以使用[ aircrack-ng ](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-cracking-wpa2-psk-passwords-using-aircrack-ng-0148366/)或[ coWPAtty](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-cracking-wpa2-psk-passwords-with-cowpatty-0148423/).

### 目录
1. 无线网络是如何被保护的？
2. 你需要什么
3. 为 Wi-fi 设置 CommView
4. 寻找目标网络并抓包
5. 等待……
6. 现在是最有趣的部分……破解！
7. 你是视觉型学习者吗？

### Step 1：无线网络是如何被保护的？

在一个被保护的无线连接中，互联网数据由被加密过的数据包所构成。这些数据包被网络密钥所加密。如果你用某种方式拿到了某个无线网络的密钥，实际上你就可以接入这个无线网。

一般来说，会有[两种主要类型的加密](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-getting-started-with-terms-technologies-0147659/)被使用。

### WEP(Wired Equivalent Privacy)

这是最基本的加密形式。已经成为一个不安全的选择，因为它有漏洞，可以被轻易的破解。虽然还是有很多人仍然使用这种加密。

### WPA (Wi-Fi Protected Access)

这是更安全的选择。这种网络的密码短语的高效破解需要使用具有通用密码的字典 换句话说，你得使用老式的排除法来获得访问权限。它的变种包括WPA-2，这是至今最安全的加密替代方案。如果密码很常见的话，也可以使用字典来破解，但是这个密码实际上是无法破解的。也就是说，除非WPA PIN仍然被启用（在许多路由器上是默认的）。

hacking WEP 密码相对较快，所以我们在这个指南将重点讨论如何破解它。如果你周围的所有网络都使用 WPA 密码，那么你需要跟着这篇指南来了解如何破解WPA Wi-Fi密码。

### Step 2：你需要什么

- **一块兼容的无线网卡：**

这是目前最大的要求。你的计算机的无线网卡必须与 CommView 兼容。这确保了无线网卡可以进入捕获数据包所必需的监视模式。[点这里](https://www.tamos.com/download/main/ca.php)查看你的无线网卡是否兼容。

- **CommView for Wi-Fi：**

这个软件将用于捕获所预设无线网卡的数据包。[点这里](https://www.tamos.com/download/main/ca.php)从他们的网站下载软件。

- [**Aircrack-ng GUI：**](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-getting-started-with-aircrack-ng-suite-wi-fi-hacking-tools-0147893/)

在捕获数据包之后，这个软件会执行实际的破解。[点这里](http://www.aircrack-ng.org/)从他们的网站下载软件。

- **一点耐心也是需要的**

### Step 3：为 Wi-fi 设置 CommView

- 从官网下载 CommView for Wi-Fi 的 zip 文件。解压文件并运行 setup.exe 来安装 CommView for Wi-Fi。当 CommView 首次打开时，它会运行驱动程序安装指南。按照提示给你的无线网卡安装驱动程序。
- 运行 CommView for Wi-Fi。
- 点击应用窗口左上角的 play 图标

开始扫描无线网络

CommView 现在开始一个一个信道地扫描无线网络。几分钟后，你将会获得一张长长的包含无线网络和他们安全类型及信号的列表。现在是时候选择你的目标网络了。

### Step 4：寻找目标网络并抓包

在选择目标无线网络之前，请牢记几件事：

- 这个向导只支持 WEP 加密网络，所以请确认你选择的网络名后面有 WEP。如果你需要破解 WPA 加密网络，可以来看看[这篇向导](https://null-byte.wonderhowto.com/how-to/hack-wpa-wifi-passwords-by-cracking-wps-pin-0132542/)
- 选择一个信号最好的网络。
- 每个网络的详细信息都在右栏中。
- 确保你选择的 WEP 网络具有最低的 dB（分贝）值。
- 右键单击所需的网络，然后单击复制 MAC 地址。
- 切换到顶部的规则选项卡。
- 在左侧选择 MAC 地址。
- 启用 MAC 地址规则。
- 为“Action”选择“capture”，并为“Add record”选择“both”。
- 现在粘贴事先复制的MAC地址在下面的框中。

我们只需要捕获数据包进行破解。因此，在窗口顶部的栏上选择D并取消选择M（管理数据包）和C（控制数据包）。

现在你必须保存这些数据包，以便以后可以破解。按照下列步骤去做：

- 到顶部的日志选项卡并启用自动保存。
- 将最大目录大小设置为2000。
- 将平均日志文件大小设置为20。

### Step 5：等待……

警告！现在是无聊的部分～

注意：捕获足够的数据包所需的时间取决于信号和网络的使用情况。你应该捕获的数据包的最小数量应该是差不多10万个信号。

当你觉得你有足够的数据包（至少100,000个数据包）时，你需要导出它们。

- 转到 log 选项卡并单击连接日志。
- 选择所有已保存的日志。
- 不要关闭CommView for Wi-Fi。
- 现在导航到保存连接日志的文件夹。
- 打开日志文件。
- 选择 File- Export-Wire shark tcpdump 格式并选择合适的目标文件夹。
- 这会将具有.cap扩展名的日志保存到该位置。

### Step 6：现在是最有趣的部分……破解！

- 下载并解压 Aircrack-ng 的 zip 文件。
- 打开文件夹并导航到“bin”。
- 运行 Aircrack-ng GUI。
- 选择 WEP。
- 打开你之前保存的.cap文件。
- 点击启动。
- 在命令行下输入目标无线网络的索引号。
- 稍等片刻。如果一切顺利，无线密钥会被显示出来。

你也可能会收到尝试使用更多数据包的请求。遇到这种情况就等待更多的数据包被捕获，并重复捕获数据包后执行的步骤。

祝你好运！

### Step 7：你是视觉型学习者吗？

万一你不明白，你可以自己做实验试试。

[原文](https://mods-n-hacks.gadgethacks.com/how-to/crack-wi-fi-passwords-for-beginners-0139793/)



