---
layout: post
title: "学习使用 Github 素材服务器"
date: 2018-04-25
excerpt: "GitHub Assets Server"
tag:
- GitHub
comments: true
---


刚开始在 GitHub 上搭建 Blog 时遇到了个问题，上传到仓库里的图片无法在网页中被加载出来。朋友们都推荐我用七牛的图床，但由于我懒得去注册，所以决定另找办法。

百度后了解到 GitHub 有素材服务器，可以用来储存用户上传的素材文件。


## 使用方法

使用方法很简单，只需要正常地上传文件，然后构造出 URL 就可以使用了。

URL 格式为：

	仓库地址/raw/图片/分支名/路径

如果还是不懂的话请看下面的栗子

## 栗子

比如图片在 GitHub 上的地址是

    https://github.com/Aquilao/Blog/blob/master/assets/img/learn_github_assets_server-img/Fuck_off.png

那就把中间的 `blob` 改成 `raw`

	https://github.com/Aquilao/Blog/raw/master/assets/img/learn_github_assets_server-img/Fuck_off.png

就成功了

![](https://github.com/Aquilao/Blog/raw/master/assets/img/learn_github_assets_server-img/Fuck_off.png)

P.S. 其实这张图片的真实地址为

    https://raw.githubusercontent.com/Aquilao/Blog/master/assets/img/Elevation_of_privilege-Reijie_gateway-img/1.png

raw.githubusercontent.com 就是素材服务器的域名
