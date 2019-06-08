---
layout: post
title: "Metasploit 基础，Part 1（入门与概述）"
date: 2018-2-4
excerpt: "Metasploit 入门与概述"
translation: ture
tags: []
comments: true
---

欢迎回来，我的新手 hackers！Weekly-translation_column-Plan-0x01-WiFi_Hacking

我已经写了很多关于使用 [Metasploit](https://null-byte.wonderhowto.com/search/metasploit/) 进行 hacking 的教程，包括如何[不留下任何证据](https://null-byte.wonderhowto.com/how-to/hack-like-pro-use-metasploits-psexec-hack-without-leaving-evidence-0149027/)和[探索其内部体系](https://null-byte.wonderhowto.com/how-to/hack-like-pro-exploring-inner-architecture-metasploit-0151006/)。另外，还有我的 [Metasploit 命令](https://null-byte.wonderhowto.com/how-to/hack-like-pro-ultimate-command-cheat-sheet-for-metasploits-meterpreter-0149146/)和 [hacking 脚本](https://null-byte.wonderhowto.com/how-to/hack-like-pro-ultimate-list-hacking-scripts-for-metasploits-meterpreter-0149339/)的备忘单。

通过这篇指南，我将开始一个循序渐进的 Metasploit 学习系列。 第一部分将介绍Metasploit的基础知识，对于那些刚接触的人，以及对那些忘记而希望复习的人的。考虑到这一点，这将是一个快餐式的第一课，关于如何使用地球上最强大的 hacking 平台。

### Metasploit 的背景及安装

Metasploit 是由 HD Moore 在2003年作为一个开源项目开发的。最初是用 Perl 编写的，Metasploit 在2007年被 Ruby 完全重写。2009年，它被 Rapid7（一家也是生产漏洞扫描器 Nexpose 的 IT 安全公司）购买。

Metasploit 最新的是4.9.3版本，它被收录于我们的 [Kali Linux](https://null-byte.wonderhowto.com/how-to/hack-like-pro-getting-started-with-kali-your-new-hacking-system-0151631/) 中，也被内置到 [BackTrack](https://null-byte.wonderhowto.com/how-to/hack-like-pro-getting-started-with-backtrack-your-new-hacking-system-0146889/) 中。对于那些使用其他版本的 Linux 或 Unix（包括 Mac OS ）的用户，可以从 [Rapid7 的网站](https://www.rapid7.com/products/metasploit/download/)下载 Metasploit。

对于那些使用 Windows 的人来说，你也可以从 Rapid7 下载到它，但是我不建议在 Windows 中运行 Metasploit。虽然你可以下载并安装它，但是这个 hacking 框架的一些功能并没有转移到 Windows 操作系统上，而且我们 Null Byte 的许多 hack 都不能在Windows平台上进行。

![Download](https://img.wonderhowto.com/img/87/23/63540586942218/0/hack-like-pro-metasploit-for-aspiring-hacker-part-1-primer-overview.w1456.jpg)

Metasploit 现在有多种产品，其中包括 Metasploit Pro（完整的商业版本）和内置于 Kali 的社区版本，并保持免费。我们将把所有的精力集中在社区版上，因为我很清楚你们大多数人不会购买价值30,000美元的专业版。

### 如何使用Metasploit

Metasploit可以以多种方式访问或使用。最常用的方法也是我使用的方法是交互式的 Metasploit 控制台。可以通过在 Kali 的命令行输入 msfconsole 以启动。当然还有其他几种方法。

### Msfcli

（译者注：Msfcli 已被弃用，这部分跳过）

### Armitage

如果你想用 GUI（图形用户界面）来使用 Metasploit，至少有几个选项可以使用。首先，Raphael Mudge 开发了Armitage（是指一个网络空间科幻作品中的主要角色，Neuromancer - 任何喜欢科幻小说的黑客必读）。

要在 Kali 启动 Armitage，只需输入：

	armitage

![armitage](https://img.wonderhowto.com/img/64/36/63540318703435/0/hack-like-pro-metasploit-for-aspiring-hacker-part-1-primer-overview.w1456.jpg)

如果 Armitage 无法连接，请尝试以下替代命令：

	service start postgresql
	service start metasploit
	service stop metasploit

![armitage](https://img.wonderhowto.com/img/62/48/63540318848263/0/hack-like-pro-metasploit-for-aspiring-hacker-part-1-primer-overview.w1456.jpg)

Armitage 是 Metasploit 上覆盖的 GUI，在 C/S 结构中运行。你可以将 Metasploit 作为服务器启动，Armitage 成为客户端，从而使你能够通过功能全面且直观的 GUI 完全访问 Metasploit 的功能。如果你真的需要一个图形用户界面来感觉舒服，我不想阻止你使用 Armitage，但掌握命令行是任何自我尊重的 hacker 的必要条件。

### 模块

Metasploit有六种不同类型的模块。分别是：

1. payloads
2. exploits
3. post
4. nops
5. [auxiliary](https://null-byte.wonderhowto.com/how-to/hack-like-pro-exploring-metasploit-auxiliary-modules-ftp-fuzzing-0155574/)
6. encoders

payloads 是我们将在被黑客入侵的系统上留下的代码。有些人称这些监听器，rootkit 等等。在 Metasploit 中，它们被称为payloads。这些 payloads 包括命令 shell，Meterpreter 等。payloads 可以分段，内联，NoNX（绕过某些现代 CPU 中的不执行功能），PassiveX（绕过受限制的出站防火墙规则）以及IPv6等等。

Exploits 是利用系统中的漏洞或缺陷的 shellcode。这些是特定于操作系统、特定服务包（SP）、特定服务、特定端口，甚至是特定应用。它们是按操作系统分类的，所以 Windows 的 exploit 无法在 Linux 操作系统中作用，反之亦然。

Post是我们可以使用系统后期开发的模块。

Nops是无操作的简称。 在x86 CPU中，通常用十六进制0x90表示。它只是意味着“什么都没做”。这对创建缓冲区溢出至关重要。我们可以使用 show 命令查看 nops 模块。

	show nops

![show nops](https://img.wonderhowto.com/img/37/27/63540342181779/0/hack-like-pro-metasploit-for-aspiring-hacker-part-1-primer-overview.w1456.jpg)

Auxiliary 包括许多不符合任何其他类别的模块（695）。这些包括诸如模糊测试器，扫描器，拒绝服务攻击等等。查看我的关于 fuzzers 模块的文章，以获取有关 fuzzers 的更多信息。

Encoders 是使我们能够以各种方式对我们的 payloads 进行编码以使其他安全设备通过杀毒软件的模块。通过输入：

	show encoders

![show encoders](https://img.wonderhowto.com/img/45/66/63540342301107/0/hack-like-pro-metasploit-for-aspiring-hacker-part-1-primer-overview.w1456.jpg)

正如你所看到的，Metasploit 中内置了许多编码器。 我的最爱之一是 [shikata_ga_nai](https://null-byte.wonderhowto.com/how-to/hack-like-pro-change-signature-metasploit-payloads-evade-antivirus-detection-0149867/)，它可以让我们对 payload 进行 XOR 运算 ，以使杀毒软件和安全设备无法检测。

### 搜索

自从Metasploit 4发布以来，Metasploit增加了搜索功能。以前，必须使用 msfcli 和 grep 来搜索你正在寻找的模块，但是现在 Rapid7 已经添加了搜索关键字和功能。Metasploit 发展迅速，搜索功能的增加非常及时，因为简单的眼球搜索和 grep 搜索不足以搜索1400多个漏洞。

搜索关键字使我们能够进行简单的关键字搜索，但同时也使我们在搜索中更加精确。例如，我们可以使用 type 关键字来定义我们要搜索的模块的类型。

	search type:exploit

![search](https://img.wonderhowto.com/img/65/02/63540321278685/0/hack-like-pro-metasploit-for-aspiring-hacker-part-1-primer-overview.w1456.jpg)

当我们这样做的时候，Metasploit 反回了所有1295个漏洞。但这并没有什么实际作用。

如果我们知道我们想要攻击运行 Solaris（Sun的UNIX）的 Sun Microsystems机器，我们可能需要将我们的搜索改进为只有 solaris 漏洞，然后才能使用 platform 关键字。

	search type:exploit platform:solaris

![search](https://img.wonderhowto.com/img/99/48/63540321754622/0/hack-like-pro-metasploit-for-aspiring-hacker-part-1-primer-overview.w1456.jpg)

现在我们已经将搜索范围缩小到仅针对 Solaris 操作系统的攻击。

为了进一步优化我们的搜索，我们假设我们想要攻击 Solaris RPC（sunrpc），我们只想看到攻击这个特定服务的攻击。我们可以将关键字“sunrpc”添加到我们的 serach 中，如下所示：

	search type:exploit platform:solaris sunrpc

![search](https://img.wonderhowto.com/img/83/94/63540322466169/0/hack-like-pro-metasploit-for-aspiring-hacker-part-1-primer-overview.w1456.jpg)

就像你所看到的，这个缩小范围的结果只有五个 exploit 模块！

Metasploit 有许多尚未开发的功能，所以我将继续这个 Metasploit 系列来探索最简单到最复杂的功能。与此同时，去尝试一些我在很多文章列出的 Metasploit hacks，并不断回到这个系列的 Metasploit 学习如何像一个大师一样 hack ！

[原文](https://null-byte.wonderhowto.com/how-to/hack-like-pro-metasploit-for-aspiring-hacker-part-1-primer-overview-0155986/)
