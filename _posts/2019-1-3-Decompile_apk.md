---
layout: post
title: "移动安全基础 —— Windows 下 Android apk 的反编译、重打包及签名"
date: 2019-1-5
excerpt: "Android apk 测试学习笔记 Part 1"
tag:
- Android apk
- Mobie Seurity Base
comments: true
---


由于工作的原因，接触到一些移动安全的业务，这里把我的笔记整理了一下分享出来，希望能对读者有所帮助（如果有读者的话~）

## 0x00 apk 解包

> Android应用程序包（英语：Android application package，APK）是Android操作系统使用的一种应用程序包文件格式，用于分发和安装移动应用及中间件。一个Android应用程序的代码想要在Android设备上运行，必须先进行编译，然后被打包成为一个被Android系统所能识别的文件才可以被运行，而这种能被Android系统识别并运行的文件格式便是“APK”。 一个APK文件内包含被编译的代码文件(.dex 文件)，文件资源（resources）， assets，证书（certificates），和清单文件（manifest file）。
>
> APK 文件基于 ZIP 文件格式，它与JAR文件的构造方式相似。它的互联网媒体类型是：application/vnd.android.package-archive

以上摘自 Wiki。

要是太长不想看的话，只需要知道 apk 其实是 zip，用解压缩 zip 的解压软件即可。

## 0x01 使用 dex2jar 逆向 classes.dex

[dex2jar 下载地址](https://sourceforge.net/projects/dex2jar/)

在解压后的文件夹里面有一个 classes.dex

> classes.dex: classes文件通过DEX编译后的文件格式，用于在Dalvik虚拟机上运行的主要代码部分。

我们可以用 dex2jar 把 classes.dex 变成 jar 文件。

使用命令

    <d2j-dex2jar.bat_path> <classes.dex_path>

即可生成 classes-dex2jar.jar 在你的当前目录下。

P.S. <d2j-dex2jar.bat_path> 指 d2j-dex2jar.bat 文件的路径，<classes.dex_path> 指 classes.dex 文件的路径。

变成 jar 文件就好办了，我们可以用 jd-gui 来直接读其中的 Java 代码。

[jd-gui 下载](http://jd.benow.ca/)

## 0x02 使用 Apktool 进行反编译

[Apktool 下载及使用方式](https://ibotpeaches.github.io/Apktool/install/)

官方提供的下载及使用方式写的很详细，就不介绍了，我们直接上手。

进入放有 apktool.jar 和 apktool.bat 的文件夹

使用命令

    apktool d -f <apk_path> -o <folder_path>

即可解包该 apk 在指定的文件夹下。

P.S. <apk_path> 指待反编译 apk 的路径，<folder_path> 指把反编译的文件输出的文件夹

我们可以对反编译出来的 smali 代码或其他文件进行修改，另外反编译出的 AndroidManifest.xml 文件可以被读取，可能会在里面发现一些安全隐患。

## 0x03 使用 Apktool 进行打包

Apktool 反编译得到的文件可以重新打包

    apktool b <folder_path> -p <apk_path>

## 0x04 生成签名

我们要先生成 keystore 文件

    keytool -genkey -alias <keystore_alias> -keyalg RSA -validity <effective_time> -keystore key.keystore

<keystore_alias> 是 keystore 文件的别名，然后加密算法我们选了 RSA ，<effective_time> 是签名有效时间，单位是天，然后输出的名称为 key.keystore。

## 0x05 对 apk 进行签名

现在要使用我们刚生成的 keystore 文件对打包好的 apk 进行签名

    jarsigner -verbose -keystore <keystore_path> -signedjar <signed_apk_path> <unsigned_apk_path> <keystore_alias>

<keystore_path> 是指 keystore 文件存放的路径，<signed_apk_path> 是指签名后的 apk，<unsigned_apk_path> 是指未签名的 apk，<keystore_alias> 是指之前生成 keystore 文件时所使用的别名

还有就是如果 apk 已经被加固过反编译后重打包之后生成的 apk 会比原先小很多...

## 0x06 参考

1. [Android APK反编译就这么简单 详解](https://blog.csdn.net/vipzjyno1/article/details/21039349)
