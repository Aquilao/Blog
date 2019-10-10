---
layout: post
title: "敏感信息泄露 Part 1 —— 源码泄露"
date: 2019-3-31
excerpt: "常见源码泄露的种类与利用工具"
tag:
- Web Seurity Base
comments: true
---

## 0x00 概述

源码泄露并非漏洞，而是在网站管理员粗心大意的情况下导致某些敏感文件可以被攻击者获取到，从而进一步获取源码。

攻击者可能会通过审计获取到的源码，进一步对网站继续渗透。

## 0x01 git 源码泄露

使用 git init 初始化 git 项目后会在项目目录会存在一个`.git`文件夹

可以用 Google Hack 找一些来实验（接下来其他的都可以用差不多的方法来寻找）

    intitle: index of /.git

利用工具: lijiejie - [GitHack](https://github.com/lijiejie/GitHack)

**2019.10.10 更新：之前有一次遇到一道 CTF，用 GitHack 提取不完全，后面改用[Git_Extract](https://github.com/gakki429/Git_Extract)才获取到，建议使用后者**

## 0x02 svn 源码泄露

和 git 源码泄露一样，在根目录会存在一个`.svn`文件夹。

利用工具: admintony - [svnExploit](https://github.com/admintony/svnExploit)

## 0x03 源码备份泄露

站点目录里可能存在`.zip`、`rar`、`7z`、`tar`、`gz`、`bz2` 等后缀的压缩包文件。

特别是根目录，可能有整个网站的源码压缩包备份。

## 0x04 .DS_Store 文件泄露

`.DS_Store`文件是 Mac 下 Finder 用来保存如何展示 文件/文件夹 的文件。

如果存放网站源码的目录存在该文件的话，可以被遍历下载。

利用工具: lijiejie - [ds_store_exp](https://github.com/lijiejie/ds_store_exp)

## 参考

1. [谈谈源码泄露 · WEB 安全](https://blog.csdn.net/GitChat/article/details/79014538) - GitChat技术杂谈
2. [SVN源码泄露利用原理+实战](https://blog.csdn.net/qq_36869808/article/details/88846945) - Bubble_zhu