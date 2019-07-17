---
layout: post
title: "恶意代码上传与解析 Part 3 —— File Inclusion 的利用"
date: 2018-5-17
excerpt: "File Inclusion 基础利用"
tag:
- File Inclusion
- 恶意代码上传与解析
- Web Seurity Base
comments: true
---

当程序语言是 PHP 时，我们还可以考虑用文件包含来解析恶意代码（因为相对其他语言来说还是在 PHP中较为常见，当然其他语言里也有）。本文就用 PHP 来举栗子，大概介绍一下 FI。

## 0x00 概述

FI(File Inclusion) 即文件包含漏洞，是指后端脚本没有仔细验证所引入的文件名，攻击者操纵参数而导致敏感文件泄漏或恶意代码注入。

可由其包含的文件位置分成两类 LFI(Local File Inclusion) 和 RFI(Remote File Inclusion)。

### LFI(Local File Inclusion) & RFI(Remote File Inclusion)

LFI 即本地文件包含漏洞，顾名思义，包含的是本地的文件。而 RFI 是远程文件包含漏洞，包含的是远程主机中的文件。

相对 LFI 来说 RFI 危害要更大，因为远程主机中的文件是攻击者可控的（比如攻击者在自己的跳板机里准备了恶意代码），而在一台还未攻陷的机器中操纵文件当然更难。

好在 RFI 比较少见，因为有些操作要求在 php.ini 中打开allow_url_fopen 和 allow_url_include 选项，然而后者默认是关闭的。然而 LFI 只需要 allow_url_fopen 选项打开就行了，由于该选项是默认打开的，所以只要开发人员稍有不慎就会产生 LFI。这两个选项的详情会在下节讲到。

有 RFI 的应用基本上会有 LFI，具体原因见上。



## 0x01 相关配置

与 FI 相关的两个选项是 allow_url_fopen 和 allow_url_include，下面引用 PHP 手册里面的内容介绍一下这两个选项。

![php.ini 默认配置](https://github.com/Aquilao/Blog/raw/master/assets/img/file_inclusion-img/ini.png)

> allow_url_fopen boolean
>
>    本选项激活了 URL 形式的 fopen 封装协议使得可以访问 URL 对象例如文件。默认的封装协议提供用 ftp 和 http 协议来访问远程文件，一些扩展库例如 zlib 可能会注册更多的封装协议。
>
>  allow_url_include boolean
>
>    This option allows the use of URL-aware fopen wrappers with the following functions: include, include_once, require, require_once.

allow_url_include 没给出翻译，我大概翻译一下

> allow_url_include boolean
>
>   本选项允许使用带有以下的函数的 URL 形式 fopen 封装协议：include, include_once, require, require_once。



## 0x02 危险语句

在 PHP 中，文件包含使用的只有四条语句。

1. include：在脚本执行前读入文件,读取失败后产生警告（E_WARNING），不终止程序
2. require：在脚本执行到这条语句时读入文件，读取失败后产生致命错误（E_COMPILE_ERROR ）并终止程序
3. include_once：与 include 类似，文件代码已包含的话则不会再次包含
4. require_once：与 require 类似，文件代码已包含的话则不会再次包含

我们接下来全部使用 inlude 举栗子。



## 0x03 利用方式

FI 的利用方式可以简单的概括为两种，**代码注入**和**敏感文件读取**，不过为了达到目的可能要用上一些姿势。

### 代码注入

include 等语句读取文件后会尝试解析，如果文件不是 PHP 代码的话会直接输出，这个可以用来读取一些敏感文件，而且如果文件是 PHP 代码的话就会被执行，所以说文件包含算是一种代码注入的手段。

值得一提的是无论文件扩展名是啥，这些语句都会尝试去解析文件，所以只要文件是 PHP 代码的话，无论用什么扩展名都会被解析。

我们写个简单的带有文件包含漏洞的 PHP 脚本

    <?php
        include $_GET[page];
    ?>

再准备几个不同扩展名的文件，其内容都是

    <?php
        phpinfo();
    ?>

比如我准备了".php"、".txt"、".7z"三个文件进行测试

![info.php](https://github.com/Aquilao/Blog/raw/master/assets/img/file_inclusion-img/info_php.png)
![info.txt](https://github.com/Aquilao/Blog/raw/master/assets/img/file_inclusion-img/info_txt.png)
![info.7z](https://github.com/Aquilao/Blog/raw/master/assets/img/file_inclusion-img/info_7z.png)

".php"就不用说了，但其他的扩展名的文件也成功解析了，可见的确能解析任意扩展名的 PHP 代码。

如果被包含的 PHP 代码不是 phpinfo() 而是恶意代码呢，所以我们可以通过文件上传或其他漏洞把后门上传上去的话可以尝试用 LFI 来解析它。

比如我们可以上传一个图马

![png_add](https://github.com/Aquilao/Blog/raw/master/assets/img/file_inclusion-img/png_add.png)

然后再包含它

![fuck](https://github.com/Aquilao/Blog/raw/master/assets/img/file_inclusion-img/fuck.png)


### 读取敏感文件

读取敏感文件的话就简单的用 /etc/passwd 举栗子吧。

![passwd](https://github.com/Aquilao/Blog/raw/master/assets/img/file_inclusion-img/passwd.png)

只要文件有可读权限且程序没有对目录进行限制的话，整台电脑上的文件是可以随便读的。

那如果我们想得到 PHP 源码呢？那也有办法，不过需要用到其他姿势，后面会讲




## 0x04 封装协议(Wrappers) 在 FI 中的利用

在 FI 的利用中经常要使用到某些 PHP 封装协议，限于篇幅，不能逐一详细介绍，具体可以阅读[支持的协议和封装协议](http://php.net/manual/zh/wrappers.php)

一些常用的会在下面结合利用方法讲到。

### http(s)://

就是和你想的一样简单，使用 http(s)// 协议的 GET 方法读取服务器上的文件，受 allow_url_fopen 和 allow_url_include 的影响。

使用方式就不用我介绍了吧，相信大家都会。另外有个?截断会在后面提到。

### php://input

当 allow_url_include 选项打开的时候，我们就可以借助一些封装协议来包含外部输入流。

php://input 可以获取 POST 请求的原始数据，我们可以利用文件包含来执行其获取到的 POST 请求。

比如执行 phpinfo()

![input](https://github.com/Aquilao/Blog/raw/master/assets/img/file_inclusion-img/input.png)

要是再配上 system() 函数和 nc 的话我们就能获得一个反向 shell了。

![input_shell](https://github.com/Aquilao/Blog/raw/master/assets/img/file_inclusion-img/input_shell.png)
![re_shell](https://github.com/Aquilao/Blog/raw/master/assets/img/file_inclusion-img/re_shell.png)

### php://filter

php://filter 是一种元封装器，设计用于数据流打开时的筛选过滤应用。它不会受到 allow_url_fopen 和 allow_url_include 的影响。

由于只要被包含的文件是 php 代码的话就会被解析，所以想要得到 php 源码的话就不能让该文件直接输出，我们可以使用到 php://filter 元封装器对文件进行 base64 编码

格式为

    php://filter/read=convert.base64-encode/resource=[path]

比如读取刚才的 info.php

![base64](https://github.com/Aquilao/Blog/raw/master/assets/img/file_inclusion-img/base64.png)

再将得到的字符串进行 base64 解码就能得到 PHP 源码了

![base54_decode](https://github.com/Aquilao/Blog/raw/master/assets/img/file_inclusion-img/base64_decode.png)

P.S. 该方法属于 LFI

### data://

data:// 是个数据流封装器，受 allow_url_include 选项影响。

我们可以把要执行的 php 语句以流的方式让其包含

![data](https://github.com/Aquilao/Blog/raw/master/assets/img/file_inclusion-img/data.png)

在某些时候像'<'和'>'等符号会被过滤掉，这时候我们可以用 base64 编码绕过

![data_b64](https://github.com/Aquilao/Blog/raw/master/assets/img/file_inclusion-img/data_b64.png)

### file://

file:// 是 PHP 使用的默认封装协议，不受 allow_url_fopen 和 allow_url_include 的影响，可以读取本地文件，没什么好讲的。

### phar://

不受 allow_url_fopen 和 allow_url_include 影响，属于 LFI，通常被用来绕过限定扩展名的情况，这种情况的描述见下一节。

假如只允许上传后缀名为".jpg"的文件，我们可以把"info.php"压缩为"info.zip"，然后修改扩展名为".zip"后上传。

![phar](https://github.com/Aquilao/Blog/raw/master/assets/img/file_inclusion-img/phar.png)



## 0x05 截断

开发者出于让文件包含所使用的参数看起来更不显眼、防止任意文件读取、或防止攻击者通过 LFI 配合文件上传来 getshell等原因往往会固定扩展名

比如

    <?php
        include $_GET[page] . '.php'
    ?>

访问的时候就不用输入'.php'的后缀了

相对上面提到的 phar:// 之外，我们更多的是使用截断

### 00截断

具体方法就是照常构造 payload 然后在后面加上 `%00` 就行了

在 php < 5.3.4 & magic_quotes_gpc = Off 时可用（由于我的环境是 PHP 7 所以就不演示了～）

### ?截断

当 00截断用不了的时候我们还可以使用`?`来截断，前提是 allow_url_fopen 和 allow_url_include 是开启的，因为我们要使用到 http(s)://

![http](https://github.com/Aquilao/Blog/raw/master/assets/img/file_inclusion-img/http.png)

这会让程序误以为'?'后的'.php'是参数

### 超长路径截断

在 php < 5.2.8 情况下我可通过超长路径来截断后面的内容。

因为在 Windows 下文件路径最多是 256 ，而 Linux 则是 4096，我们只需要在要包含的文件后面疯狂的加"./"就行了



## 0x06 防御方式

1. 最安全的方法是关闭 allow_url_fopen 和 allow_url_include 选项，不过太不方便了。
2. 尽量不要使用动态包含，因为这可能会导致所包含的文件用户可被用户控制
3. 可以考虑给输入设置白名单，防止包含意料之外的文件
4. 限制路径



## 0x07 总结

对 File Inclusion 的利用我们只需要明白两点

1. 只要我们有读权限，所有文件都可以被读取
2. 只要服务器上某个已知路径文件可控，我们就能包含然后 getshell

到这里，《恶意代码上传与解析》总算告一段落了。我们花了三篇文章来探讨“从如何绕过上传检测”到“如何解析恶意代码”这个问题，期间也有提到一些相关问题和知识。果然写 Blog 是一个记自己成长的办法，相对于记笔记来说，写一篇文章更需要思考如何复现并描述一个漏洞、解释一个概念，这个过程也是一个自我成长过程。接下来我会继续把我的笔记整理成文章，期间构筑自己的知识体系过程，如果读者对文章有意见或建议的话请务必给我发邮件或 Issues。

## 0x08 参考

1. [使用远程文件](http://php.net/manual/zh/features.remote-files.php) - php manual
2. [支持的协议和封装协议](http://php.net/manual/zh/wrappers.php) - php manual
3. [PHP 手册 函数参考 文件系统相关扩展 文件系统 运行时配置](http://php.net/manual/zh/filesystem.configuration.php) - php manual
4. [详解php文件包含原理](https://bbs.ichunqiu.com/forum.php?mod=viewthread&tid=28688&highlight=%E6%96%87%E4%BB%B6%E5%8C%85%E5%90%AB) - rosectow
5. [LFI、RFI、PHP封装协议安全问题学习](http://www.cnblogs.com/LittleHann/p/3665062.html) - Han Zheng
