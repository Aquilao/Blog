---
layout: post
title: "Metasploit 基础，Part 2 (关键字)"
date: 2018-2-11
excerpt: "Metasploit 关键字"
translation: ture
tags: []
comments: true
---


欢迎回来，我的新手 hacker 们！

我最近开始了一个关于使用 [Metasploit 的系列文章](https://null-byte.wonderhowto.com/how-to/metasploit-basics/)，目标是教你们最基本的使用这个非常强大的黑客工具，并逐步向更高级的功能前进。

在我的第一篇文章 —— Metasploit 的安装中，我向你们展示了使用 Metasploit 的各种方法，从 msfcli 到 msfconsole，再到基于 gui 的 Armitage。此外，我还概述了各种模块，包括 exploits，payloads，和 encoders。最后，我们学习了在 Metasploit 中包含的一些基本搜索功能，以帮助你找到特定 exploits，payloads，post-exploitation 模块, scanners，encoders等等。

在这第二篇教程中，我们将会介绍一些在 Metasploit 中可以使用的基本命令。尽管 Metasploit 框架对没使用过的人来说是令人生畏的，但它实际上是一个非常简单的系统利用框架。如果你可以学习一些关键字和技术，那么就可以使用 Metasploit 来攻击任何系统了。

### Metasploit 关键字

在 Metasploit 中使用几个关键字可以帮助你操纵和管理这个强大的软件。让我们看一些最基本且必要的 Metasploit 命令。虽然说这远非一个完整的 Metasploit 关键字和命令列表，但是它包含了在你获得更多的经验需要的 Metasploit 执行功能的基本命令。

如果你已经在 Metasploit 中有一些经验，并且想要 meterpreter 的命令，可以查看我的 [meterpreter 命令备忘单](https://null-byte.wonderhowto.com/how-to/hack-like-pro-ultimate-command-cheat-sheet-for-metasploits-meterpreter-0149146/)。

### 1. Show

“Show”是 Metasploit 中最基本的命令之一。它可以用来显示模块，比如显示 payloads、显示 exploits 等等。但是，一旦我们选择了 exploits，它也可以用来显示选项。

当我们选择了一个 exploit 时，“show”命令会变得非常敏感，所以如果我们在选择 exploit 之前键入“show payload”，它将显示所有的 payload。如果我们在选择了一个 exploit 后输入“show payload”，它只会显示将使用该 exploit 的 payload。

例如，当我们想要查看看上去无害却实际上却带有后门的 PDF 时需要设置的所有选项时，我们使用下面的“show options”命令。

![show](https://img.wonderhowto.com/img/91/98/63542919810362/0/hack-like-pro-metasploit-for-aspiring-hacker-part-2-keywords.w1456.jpg)

### 2. Help

“help”命令将提供给你一个有限的命令列表，可以在 msfconsole 中使用。如果你丢失了这个向导，只需输入“help”来获得一些基本命令。

![help](https://img.wonderhowto.com/img/16/36/63542919592769/0/hack-like-pro-metasploit-for-aspiring-hacker-part-2-keywords.w1456.jpg)

### 3. Info

“Info”是 Metasploit 中的另一个基本命令，它使我们能够看到关于 exploit 的所有基本信息。在选择了一个 exploit 后，我们可以输入“info”，它将显示所有的选项、目标以及对该 exploit 的描述。我喜欢在我使用的任何 exploit 上键入“info”，以便查找或提醒自己它的特性和要求。

例如，这里是使用 ftp auxiliary 模块时从“info”命令输出的屏幕截图。

![info](https://img.wonderhowto.com/img/93/41/63542919606362/0/hack-like-pro-metasploit-for-aspiring-hacker-part-2-keywords.w1456.jpg)

### 4. Set

“Set”是 Metasploit 中的一个基本的关键命令/关键字。我们可以使用它来设置运行该 exploit 所需的参数和变量。这些变量可以包括 payload、RHOST、LHOST、target、URIPATH等。

在下面的屏幕截图中，我使用 [psexec 来 hack 一个系统](https://null-byte.wonderhowto.com/how-to/hack-like-pro-use-metasploits-psexec-hack-without-leaving-evidence-0149027/)，我们设置了 RHOST、LHOST、SMBUser 和 SMBPass 来 hack 系统，而不受跟踪。

![set](https://img.wonderhowto.com/img/87/82/63542919679081/0/hack-like-pro-metasploit-for-aspiring-hacker-part-2-keywords.w1456.jpg)

### 5. Back

当我们处理一个特定的模块或选择错误的模块时，我们可以使用“back”命令返回到 msfconsole 提示符。

例如，如果我们选择了一个 exploit，然后发现我们选错了，我们可以简单地键入“back”，然后使用“use”命令(参见下一节)来选择另一个模块。

### 6. Use

当我们决定要对目标系统使用哪个 exploit 时，我们使用“use”命令将该 exploit 加载到内存中，并准备将其发送到目标系统。在我的教程中可以找到一个例子，它[使用心脏滴血漏洞](https://null-byte.wonderhowto.com/how-to/hack-like-pro-hacking-heartbleed-vulnerability-0154708/)从运行 OpenSSL 的系统中获取内存中的信息。

![use](https://img.wonderhowto.com/img/29/67/63542919696972/0/hack-like-pro-metasploit-for-aspiring-hacker-part-2-keywords.w1456.jpg)

### 7. Exploit

在选择了我们的 exploit，设置了所有的变量，并选择了我们的 plyload 之后，我们最后要做的就是键入“exploit”命令。这将利用 payload 和我们可能设置的变量对目标机器进行攻击。

在我的指南中可以找到这样一个例子：在一个看起来无害的 [Word 文档中创建一个exploit](https://null-byte.wonderhowto.com/how-to/hack-like-pro-hack-windows-7-see-whether-your-girlfriend-is-cheating-not-0151015/)，然后发送给你的女朋友，看看她是否在骗你。

![exploit](https://img.wonderhowto.com/img/80/03/63542919714784/0/hack-like-pro-metasploit-for-aspiring-hacker-part-2-keywords.w1456.jpg)

### 8. Sessions

“sessions”命令用于列出或设置会话。当使用-l(list)选项时，它将列出所有打开的会话。当使用一个数字(“sessions -1”)时，它告诉 Metasploit 激活第一个会话。

Metasploit允许我们在同一个系统上运行多个会话，或者在多个系统上运行多个会话。使用“sessions”命令，我们可以找到这些打开的会话并切换到或激活它们。

你可以在我的指南中找到这方面的例子：在目标系统上创建一个自动重新连接的持久后门，如下图所示。

![sessions](https://img.wonderhowto.com/img/78/70/63542919732159/0/hack-like-pro-metasploit-for-aspiring-hacker-part-2-keywords.w1456.jpg)

### 9. Exit

当你想离开 msfconsole 时，我们只需要简单的输入"exit"就可以回到我们的 linux shell 中了。

![exit](https://img.wonderhowto.com/img/07/38/63542919746691/0/hack-like-pro-metasploit-for-aspiring-hacker-part-2-keywords.w1456.jpg)

这些为你提供了一个基本的命令集，使你能运行 Metasploit 并进行 hack。在以后的教程中，我们将讨论 payloads 类型、高级命令、使用全局变量、高级 Meterpreter 技术，以及最终开发我们自己的  exploit。

继续回来，我的新手黑客们!

[原文](https://null-byte.wonderhowto.com/how-to/hack-like-pro-metasploit-for-aspiring-hacker-part-2-keywords-0156696/)