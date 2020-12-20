---
layout: post
title: "Kali重装小记"
date: 2017-08-12
excerpt: "作死后重装Kali的一些记录"
tag:
- Misc~
comments: true

---
昨天尝试安装NVIDIA显卡驱动的时候崩了，全力补救后只能用基于wayland的GNOME登录，进去后还有一大堆坑，所以打算重装kali。这个暑假已经是第二次重装系统了，简直哭出声。想了想有必要写一篇文章简单记录一下我定制kali的过程，免得下次重装又要花大把时间在一堆过时的资料里面找有用的东西。本文将持续更新

**P.S. 我的是kali rolling，这些配置在其他的kali里面不一定适用**

### 修改源

先查看版本

    # lsb_release -a    

我的是kali-rolling

    # vim /etc/apt/sources.list

以下都是可用的源

    #中科大
    deb http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib
    deb-src http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib

    #阿里云
    #deb http://mirrors.aliyun.com/kali kali-rolling main non-free contrib
    #deb-src http://mirrors.aliyun.com/kali kali-rolling main non-free contrib

    #清华大学
    #deb http://mirrors.tuna.tsinghua.edu.cn/kali kali-rolling main contrib non-free
    #deb-src https://mirrors.tuna.tsinghua.edu.cn/kali kali-rolling main contrib non-free

    #浙大
    #deb http://mirrors.zju.edu.cn/kali kali-rolling main contrib non-free
    #deb-src http://mirrors.zju.edu.cn/kali kali-rolling main contrib non-free

再整体更新，第一次会比较久

    # apt update && apt upgrade && apt dist-upgrade

参照博客园的一篇[文章](http://www.cnblogs.com/dunitian/p/4712852.html)

### 安装Google拼音输入法

    # apt-get install fcitx-googlepinyin

### 创建管理员账户

（Aquilao是我的用户名，可以改为其他名字）

    # useradd -m Aquilao

    # passwd Aquilao

    # usermod -a -G sudo Aquilao		

    # chsh -s /bin/bash Aquilao		 

    # id Aquilao		

### 安装内核头文件

    $ sudo apt install linux-headers-$(uname -r)

### 安装redshift

护眼神器，类似于现在的智能手机的护眼功能

    $ sudo apt install redshift

### 安装pip3

kali自带pip，然而它是和python2配套使用，如果要用python3进行开发还需要安装pip3

    $ sudo apt-get install python3-pip

用法和pip一样，只是需要把pip改成pip3而已

### 安装网易云音乐

**更新：因为现在网易云更新到了1.1.0版，会出现一个缺少 ibqcef1 的问题，虽然有解决方法，不过安装了之后网易云还是有诸如只有管理员权限才能运行、切歌之后系统音量突然变到最大等问题（差点把我搞聋了～）在这里不建议安装1.1.0版，替代方法就是用web版的网易云（做的还不错）或者耳机直接连手机。**

进入[网易云音乐官网](http://music.163.com/#/download) 选择linux版-deepin64位（不知道为什么，ubuntu版反而不行）

下载后cd到下载目录，然后运行命令（P.S. 版本不同包的名字也会不同，注意修改！）

    $ cd ~/下载

    $ sudo dpkg -i netease-cloud-music_1.0.0-2_amd64_deepin15.deb

会提示错误，这时我们要安装依赖

    $ sudo apt install -f

现在就可以重新尝试安装了


### 安装flash

进入[flash官网](https://get.adobe.com/flashplayer/) 选tar.gz包

下载后cd到下载目录，然后运行命令（P.S. 版本不同包的名字也会不同，注意修改！）

    $ cd ~/下载

    $ sudo tar xzvf flash_player_ppapi_linux.x86_64.tar.gz -C /usr/lib/mozilla/plugins/ -x libpepflashplayer.so

### 安装steam

进入[steam官网](http://store.steampowered.com/about/) 选择install steam now 这会为你下载最新的deb包

下载后cd到下载目录，然后运行命令（P.S. 版本不同包的名字也会不同，注意修改！）

    $ cd ~/下载

    $ sudo dpkg -i steam_latest.deb

然后就是普通的安装流程了，安装好了打开报错"You are missing the following 32-bit libraries, and Steam may not run: libc.so.6"

尝试在terminal里打开，它提示我要安装libgl1-mesa-dri:i386和libgl1-mesa-glx:i386，

	$ dpkg --add-architecture i386

然后就是一通apt install就可以运行了

先写到这里，以后继续更

### 安装xampp（Apache+MySQL+PHP+PERL集成环境）并部署DVWA

见我的另一篇文章[Debin系Linux下DVWA的安装](https://aquilao.github.io/Blog/DVWA/)

### 安装配置shadowsocks

    $ sudo apt install shadowsocks

打开

    $ sudo vim  /etc/shadowsocks.json

格式是

    {
        "server":"服务器地址",
        "server_port":服务器端口号,
        "local_address": "127.0.0.1",
        "local_port":本地端口号,
        "password":"密码",
        "timeout":300,
        "method":"加密方式",
        "fast_open": true,
        "workers": 1
    }

以该配置启动shadowsocks

    sslocal -c /etc/shadowsocks.json

### 配置proxychains实现任意应用代理

P.S. 要结合一开始配置的shadowsocks

打开

    $ sudo vim /etc/proxychains.conf

把最后一行的socks4注释掉，在下面添加上一行

    socks5 127.0.0.1 本地端口号

保存退出就可以了，要使用的时候先启动shadowsocks，再

    $ sudo proxychains 软件名

P.S. 这个方法来自freebuf，不过原网页已经挂掉了，就不贴链接了

### 安装 zsh 并 安装 oh-my-zsh

zsh 和 bash 在使用方法上可以说几乎一模一样了，相较与 bash 来说，zsh 对用户来说要的更加美观友好，所以我们选择 zsh 作为我们 shell。

安装 zsh

    sudo apt install zsh

oh-my-zsh 是 zsh 的配置文件，里面内置了不少主题

    sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

设置 zsh 默认 shell

    chsh -s `which zsh`

~/.zshrc 是我们的 zsh 配置文件，ZSH_THEME 是主题设置，我目前在用的是"bira"

### 安装 Atom

Atom 是 github 的一个文本编辑器，无论 UI 风格 还是强大且方便的插件都吸引了我，另外，Atom 很好支持 git，我们可以很方便的在里面 commit 然后 push。

在[官网](https://atom.io/)选择 Atom 的 deb 安装包然后直接安装

    sudo dpkg -i atom-amd64.deb

里面有很完善友好的新手引导，作为新手使用无压力

### 笔记本合盖不休眠

    sudo vim /etc/systemd/logind.conf

加一条

    HandleLidSwitch=lock

保存退出后重启服务

    sudo systemctl restart systemd-logind.service
    

### 第二天更新

再次作死尝试再次安装NVIDIA驱动，结果～

重启后提示先注销，结果按回车注销也没用，在其他tty里挣扎了一下

再次重启就出现“[ OK ] Started Update UTMP about System Runlevel Changes.”的错误

百度到的[解决办法](https://unix.stackexchange.com/questions/252603/debian-jessie-boot-hangs-at-started-update-utmp-about-system-runlevel-changes)使用后却没效果，然后我在 tty 里把所有名字前面带 NVIDIA 的包全卸载掉～于是电脑就恢复了～

算了，这显卡老子不要了

**声明：这篇文章里有大量网上找的资料，我已经在后面贴了链接，还有一些没贴的要不是来自百度知道，要不是来自国内外的论坛，或者是我自己折腾出的办法，如果作者希望挂上链接或修改这篇文章请Email联系我**
