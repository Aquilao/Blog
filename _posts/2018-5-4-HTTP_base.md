---
layout: post
title: "Web 安全基础 Part 1 ——  HTTP 基础"
date: 2018-5-4
excerpt: "HTTP 基础知识"
tag:
- HTTP
- Web Seurity Base
comments: true
---


## 0x00 概述

HTTP 即超文本传输协议（HyperText Transfer Protocol）是在 Web 上进行数据交换的基础。它基于 C/S 模式，是个无状态的应用层协议。

另外，HTTP 采用 URL 作为定位网络资源的标识，现在广泛使用的版本是 HTTP 1.1，默认端口为80。


## 0x01 HTTP 通信流程

HTTP 的通信流程大概有如下四步：
1. 客户端发起三次握手，建立 TCP 链接
2. 客户端向 Web 服务器的80端口发送 HTTP Request
3. Web 服务器向客户端发送 HTTP Response
4. 客户端与 Web 服务器进行四次握手，断开 TCP 链接

就这样，一次完整的 HTTP 通信就结束了。


## 0x02 HTTP Request/Response

正如我在概述里所说的，HTTP是一种**无状态**的协议，无状态是指 Web 服务器和浏览器不需要建立持久的链接。这意味着客户端向 Web 服务器发送一个请求（Request），Web 服务器向客户端发送一个响应（Response）之后链接就关闭了。

P.S. 当然这还得看 HTTP header 中的 Connection 头,想要了解更多的话可以读这篇文章————[HTTP/1.x 的连接管理](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Connection_management_in_HTTP_1.x)

服务器不能主动向客户端发送数据，只能被动的提供响应（Responce）

### HTTP Request 报文格式

HTTP 请求报文主要由请求行、请求头、消息正文3部分组成

	Method    Request-URL     Version		//请求行
	Request-headers					//请求头
							//空行
	Entity-body					//消息正文（某些情况下没有）

来看一个栗子，这是我的电脑访问百度的 HTTP Request,里面没有消息正文

    ———————————————请求行————————————————
	GET / HTTP/1.1		
	———————————————请求头————————————————
	Host: baidu.com				
	User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:52.0)    		//客户端信息
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
	Accept-Language: en-US,en;q=0.5
	Cookie: ......								//Cookie 太长给省略了～
	Connection: close							//表示只发送一次 HTTP Request 就关闭 TCP 连接
	Upgrade-Insecure-Requests: 1

### HTTP Response 报文格式

HTTP Response 与HTTP Request 类似，它由响应行、响应头和消息正文组成

	Version	Status-code	Reason-phrase		//响应行
	Response-headers				//响应头
							//空行
	Entity-body					//响应正文


栗子

    ——————————————————响应行————————————————
	HTTP/1.1 302 Moved Temporarily
	——————————————————响应头————————————————
	Server: bfe/1.0.8.18
	Date: Sun, 24 Sep 2017 08:37:13 GMT
	Content-Type: text/html					//文件类型
	Content-Length: 161
	Connection: Close
	Location: https://www.baidu.com/
	Expires: Mon, 25 Sep 2017 08:37:13 GMT			//响应时间
	Cache-Control: max-age=86400
	Cache-Control: privae

	——————————————————消息正文———————————————
	<html>
	......							//html 页面，太长省略了～
	</html>

**这节中出现的一些名词会在下文进行详细的介绍，请耐心往下看**


## 0x03 HTTP Method

常见的 HTTP Method 一共有7种：
1. GET 方法：请求一个指定资源的表示形式. 使用GET的请求应该只被用于获取数据
2. HEAD 方法：请求一个与GET请求的响应相同的响应，但没有响应体
3. POST 方法：用于将实体提交到指定的资源，通常导致在服务器上的状态变化或副作用
4. PUT 方法：用请求有效载荷替换目标资源的所有当前表示
5. DELETE 方法：删除指定的资源
6. OPTIONS 方法：用于描述目标资源的通信选项
7. TRACE 方法：沿着到目标资源的路径执行一个消息环回测试


其中 GET、HEAD、POST 方法最为常见，PUT、DELETE、CONNECT 等方法由于太过于危险而被大多数 Web 服务器禁用

OPTIONS 方法可以探测到 Web 服务器所支持的请求方法

由于 HEAD 比较节省资源，所以可以用 HEAD 方法写一些扫描器来从 HTTP Headers 中获得信息

GET 和 POST 是客户端传输数据给 Web 服务器最常用的两种方法，GET 传输的数据会被加在 URL 中，而 POST 传输的数据则会以表单等形式出现在请求正文中

关于其他 Method 可以看[HTTP 请求方法](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods)

## 0x04 HTTP Status Codes

HTTP 状态码是 Web 服务器用来告诉客户端它所发的 HTTP Request 是否已成功完成的代码，大概有五类:
1. 1xx:信息提示
2. 2xx:成功
3. 3xx:重定向
4. 4xx:客户端错误
5. 5xx:服务器错误

具体可以看这篇文章————[HTTP 响应代码](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status)


## 0x05 HTTP Headers

HTTP Haeders 是客户端和 Web 服务器在进行 HTTP 通信时传递附加信息的方法，大致可以被分为四类:
1. General header：在 Request 和 Response 里都可以被使用，但不会影响消息正文
2. Request header：包含有关要获取的资源或客户端本身更多信息
4. Response header：包含有关服务器响应的补充信息
5. Entity header：包含有关消息正文的更多信息，比如正文长度或其 MIME 类型

### 一些需要注意的 HTTP Headers

有些 HTTP Headers 在 Web 安全的学习过程中会比较重要，需要注意

### X-Forwarded-For(XFF)

简介
* 包含访问服务器的客户端 IP 及中间经过代理服务器的 IP
* 只有在通过代理服务器时才会被添加
* 修改后可在应用层有一定程度达到伪造 IP 的作用

类型

	Request header

格式

    X-Forwarded-For: <client>, <proxy1>, <proxy2>

### Referer

简介
* 包含了当前请求页面的来源页面的地址
* 跨域的时候不会被添加
* Referer 首部可能暴露用户的浏览历史，泄露隐私

类型

	Request header

格式

	Referer: <url>

### user-agent(UA)

简介
* 包含了浏览器开发商、浏览器、操作系统、渲染引擎等信息，具体可见 MDN 中的这篇文章 — [《使用用户代理字段进行浏览器检测》](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Browser_detection_using_the_user_agent)
* 有些网站的反爬虫机制会通过检查 UA 来过滤掉爬虫的请求
* 在自动化大规模扫描时使用大量不同的 UA 可以起到一定程度的伪装作用

类型

	Request header

格式

	User-Agent: <product> / <product-version> <comment>

### Content-Type

简介
* 用于定义网络文件的类型和网页的编码
* 客户端可能会通过其对上传文件进行 MIME 类型检测，我们可以通过修改其值进行绕过（以后有机会再写）

类型

	Entity header

格式

	Content-Type: <MIME-type>; <directives>

### Server

简介
* 包含了服务器操作系统、Web容器、后端语言版本等信息（当然这些信息也不可靠，说不定会遇到一位喜欢恶作剧的运维人员～）

类型

	Response header

格式

	Server: <product>

### Cookie & Set-Cookie

很重要，请看我的另一篇文章 - [《 HTTP 会话机制 》](https://aquilao.github.io/Blog/HTTP_session)

暂时就想到这些了，如果以后碰到其他的话继续更。


## 0x06 总结

HTTP 是 Web 的基础，当然也是 Web 安全的基础，在 Web 安全中处处都能见到 HTTP 的身影。经过多年的发展， HTTP 也添加了大量的安全机制，值得我们去学习。



## 0x07 参考

1. [HTTP cookies](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies) - MDN
2. [HTTP 请求方法](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods) - MDN
3. [HTTP 响应代码](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status) - MDN
4. [HTTP Headers](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers) - MDN
5. [HTTP/1.x 的连接管理](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Connection_management_in_HTTP_1.x) - MDN
6. [使用用户代理字段进行浏览器检测](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Browser_detection_using_the_user_agent) - MDN
