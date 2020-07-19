---
layout: post
title: "Web 安全基础 Part 2 ——  HTTP 会话机制"
date: 2018-6-8
excerpt: "HTTP Session"
tag:
- HTTP
- Web Seurity Base
comments: true
---

## 0x00 概述

由于 HTTP 是无状态的，所以 Web 服务器无法判断哪些 Request 是同一个客户端发送的。但业务往往要求服务器能和用户建立较为稳定持久的连接，为了解决这个问题，HTTP 引入了会话管理的概念。

## 0x01 Cookie 简介

### Cookie 是什么？

由于 HTTP 无状态，指的是每次 HTTP 通信都是独立、互不干涉的。比如你要在教务系统里查成绩，但是教务系统无法判断是不是你在操作，为了安全，教务系统只能让你每进行一步操作都输入一次密码。要是这样的话我们或许会被气到不去查成绩～，所以工程师们想了个办法来解决身份认证的问题。当客户端第一次发送 Requests 的时候，Web 服务器在 Response Header 中会给客户端一块 Cookie（Set-Cookie），客户端会把它暂时储存起来，等下次客户端发生 Request 时把 Cookie 放在 Headers 中，Web 服务器看到就知道是哪台客户端了。

除了会话管理之外 Cookie 还有用户个性化设置以及浏览器行为跟踪的作用，限于文章主题，在这里不做过多叙述。

### Session cookies 和 Permanent cookies

Cookie 有两种，分别是 Session cookies 和 Permanent cookies 。

顾名思义 Session cookies 就是在一次会话中起作用的 Cookie ，而 Permanet cookies 会在到达一个过期时间（Expires）或者经过一个保质期（Max-Age）之后失效。

### Client 和 Server 使用 Cookie 的会话过程

1. Client 发送用于认证的 HTTP Request 给 Server
2. Server 进行认证
3. 认证成功后 Server 返回带 Set-Cookie 头的 HTTP Response 给 Client
4. Client 将 Cookie 储存起来
5. 当 Client 需要进行某些操作时，发送带 Cookie 头的 HTTP Request 给 Server
6. Server 对 Cookie 进行验证
7. 验证成功后对 Client 请求进行响应
8. 循环 5-7
9. 当 Cookie 过期时(Permanet cookies)、或浏览器关闭时(Session cookies)，Cookie 失效，会话结束

## 0x02 Cookie 安全

由于 Cookie 作为客户端与 Web 服务器会话认证方式，攻击者们自然会把眼光放在上面。攻击者们常用一些社会工程学手段或者 XSS 来窃取用户的 Cookie，然后再使用获取到的 Cookie 以受害者的身份与 Web 服务器建立会话。而 CSRF 也是利用客户端与 Web 服务器会话未断开，Cookie 依然有效的前提实现的。除了攻击者们，一些网络巨头也希望能从 Cookie 中获取到用户的网络使用习惯和信息来精准投放广告。

### Secure 和 HttpOnly 标记

Secure 和 HttpOnly 是 Set-Cookie 时在末尾的两个可选标记。

Secure 标记禁止 HTTP 传输带有该标记的 Cookie，而使用更安全的 HTTPS 来传输。

HttpOnly 标记禁止 Cookie 被 Javascript 获取到，可以防止攻击者通过 XSS 来窃取用户的 Cookie。

### Domain 和 Path 标记

Domain 和 Path 是 Set-Cookie 时在末尾的两个可选标记。

Cookie 的“源”并不遵守同源策略(SOP)中“源”的定义，所以 Cookie 使用这两个标记来定义“源”（即作用域）。

Domain 标记用于指定哪些主机可以接受 Cookie。默认为当前文档的主机（不包含子域名）。如果指定了 Domain，则一般包含子域名。

Path 标记用于指定主机下的哪些路径可以接受 Cookie（该URL路径必须存在于请求URL中）。以字符 %x2F ("/") 作为路径分隔符，子路径也会被匹配。

### 第三方 Cookie 和 DNT

**2019.6.6 更新**：前两天 Firefox 更新的 [67.0.1 版本](https://www.mozilla.org/en-US/firefox/67.0.1/releasenotes/)默认启用[增强跟踪保护（ETP）](https://blog.mozilla.org/futurereleases/2019/02/20/enhanced-tracking-protection-testing-update/)，对用户隐私来说，这是项很棒的举措。

第三方 Cookie 就是指那些 Cookie 的 Domain 和页面的 Domain 不同的 Cookie。通常被用来追踪和广告投放，建议关闭。

![3P_cookie](https://github.com/Aquilao/Blog/raw/master/assets/img/HTTP_session-img/3P_cookie.png)

DNT 即 Do-Not-Track 请求，注意，这仅仅只是一个“请求”而已，服务商可以选择不遵守，但还是建议开启。

![DNT](https://github.com/Aquilao/Blog/raw/master/assets/img/HTTP_session-img/DNT.png)


## 0x03 Session 简介

Session 是对 Cookie 的一种补充。

在上面提到过，最开始 HTTP 会话是需要服务器来识别客户发送的 Cookie 的，而服务器就是靠 Session 来识别客户端所发来的 Cookie 的。

由于 Cookie 有数量和大小的限制，所以在后面的发展中，那些本来储存在 Cookie 中的用户信息被迁移到储存在服务器的 Session 中，而 Cookie 只要存储像 Session ID 那种用于维持和服务器交互的信息就可以了。

如果客户端关闭了 Cookie 的话，Session ID 会通过 URL 重写来传输 Cookie，不过相对于使用 Cookie 来说还是不太安全。

## 0x04 总结

引用知乎上的一段话：

> cookie 是存储在浏览器里的一小段「数据」，而session是一种让服务器能识别某个用户的「机制」，session 在实现的过程中需要使用cookie。

另外，Cookie 最好加密，不然可能会发生越权，就比如在上次的[锐捷易网关渗透测试](https://aquilao.github.io/Blog/Elevation_of_privilege-Ruijie_gateway/)中我们就是修改 Cookie 中的 Session ID 来达到垂直越权的目的。

## 0x05 参考

1. [HTTP cookies](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies) - MDN
2. [HTTP cookie wiki](https://en.wikipedia.org/wiki/HTTP_cookie) - wiki
3. [Session (computer science) wiki](https://en.wikipedia.org/wiki/Session_(computer_science)) - wiki
4. [Cookie与Session的区别](https://bbs.ichunqiu.com/forum.php?mod=viewthread&tid=3515&highlight=session) - 池寒
5. [cookie、session、localStorage分别是什么？有什么作用？](https://zhuanlan.zhihu.com/p/22388743) - 若愚
