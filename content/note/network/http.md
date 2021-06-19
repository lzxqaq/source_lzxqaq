---
title: "HTTP"
date: 2021-01-07T15:25:54+08:00
author: "罗泽勋"
draft: true

---

### 一、基础概念
#### 请求和响应报文
客户端发送一个请求报文给服务器，服务器根据请求报文中的信息进行处理，并将处理结果放入响应报文中返回给客户端。

请求报文结构：
* 第一行包含了请求方法、URL、协议版本；
* 接下来的多行都是请求首部 Header，每个首部都有一个首部名称，以及对应的值。
* 一个空行用来分割首部和内容主体 Body。
* 最后是请求的内容主体。

```
GET http://www.example.com/ HTTP/1.1
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8
Cache-Control: max-age=0
Host: www.example.com
If-Modified-Since: Thu, 17 Oct 2019 07:18:26 GMT
If-None-Match: "3147526947+gzip"
Proxy-Connection: keep-alive
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 xxx

param1=1&param2=2
```

响应报文结构：
* 第一行 包含协议版本、状态码以及描述，最常见的是 200 OK 表示请求成功了
* 接下来多行也是首部内容。
* 一个空行分割首部和内容主体。
* 最后是响应的内容主体。

```
HTTP/1.1 200 OK
Age: 529651
Cache-Control: max-age=604800
Connection: keep-alive
Content-Encoding: gzip
Content-Length: 648
Content-Type: text/html; charset=UTF-8
Date: Mon, 02 Nov 2020 17:53:39 GMT
Etag: "3147526947+ident+gzip"
Expires: Mon, 09 Nov 2020 17:53:39 GMT
Keep-Alive: timeout=4
Last-Modified: Thu, 17 Oct 2019 07:18:26 GMT
Proxy-Connection: keep-alive
Server: ECS (sjc/16DF)
Vary: Accept-Encoding
X-Cache: HIT

<!doctype html>
<html>
<head>
    <title>Example Domain</title>
	// 省略... 
</body>
</html>
```

#### URL 
HTTP 使用 URL（统一资源定位符）来定位资源，它是 URI 的子集，URL 在 URI 的基础上增加了定位能力。

### 二、HTTP方法
客户端发送的 请求报文 第一行为请求行，包含了方法字段。

#### GET
#### POST

### 五、具体应用
#### 连接管理
#### 1.短连接和长连接
当浏览器访问一个包含多张图片和 HTML 页面时，除了请求访问的 HTML 页面资源，还会请求图片资源。如果每进行一次 HTTP 通信就要新建一个 TCP 连接，那么开销就会很大。

长连接只需要建立一个 TCP 连接就可以进行多次 HTTP 通信。
* 从 HTTP/1.1 开始默认是长连接的，如果要断开连接，需要由客户端或者服务器提出断开，使用 `Connecton:close`；
* 在 HTTP/1.1 之前默认是短连接的，如果需要使用长连接，则使用 `Connection:Keep-Alive`。

#### 2.流水线
默认情况下，HTTP 请求是按顺序发出的，下一个请求只有在当前请求收到响应之后才会被发送。

流水线是在同一条长连接上连续发出请求，而不用等待响应返回，这样可以减少延迟。

#### Cookie
HTTP 协议是无状态的，主要是为了让 HTTP 协议尽量简单，使得它能够处理大量事务。 HTTP/1.1 引入 Cookie 来保存状态信息。

Cookie 是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器之后向同一服务器再次发起请求时被携带上，用于告知服务器两个请求是否来自同一浏览器。由于之后每次请求都会需要携带 Cookie 数据，因此会带来额外的性能开销（尤其是在移动环境下）。

#### 1.用途
* 会话状态管理（如用户登陆状态、购物车、游戏分数或其他需要记录的信息）
* 个性化设置（如用户自定义设置，主题等等）
* 浏览器行为跟踪（如跟踪分析用户行为等）

#### 2.创建过程
服务器发送的响应报文包含 Set-Cookie 首部字段，客户端得到响应报文后把 Cookie 内容保存到浏览器中。

```
HTTP/1.0 200 OK
Content-type: text/html
Set-Cookie: yummy_cookie=choco
Set-Cookie: tasty_cookie=strawberry

[page content]
```

客户端之后对同一个服务器发送请求时，会从浏览器中取出 Cookie 信息并通过 Cookie 请求首部字段发送给服务器。

```
GET /sample_page.html HTTP/1.1
Host: www.example.org
Cookie: yummy_cookie=choco; tasty_cookie=strawberry
```
#### 3.分类
* 会话期 Cookie：浏览器关闭之后它会被自动删除，也就是说它仅在会话期内有效。
* 持久性 Cookie：指定过期时间（Expires）或有效期（max-age）之后就成为了持久性的 Cookie。

#### Session
除了可以将用户信息通过 Cookie 存储在用户浏览器中，也可以利用 Session 存储在服务器端，存储在服务器端的信息更安全。

Session 可以存储在服务器上的文件、数据库或者内存中。也可以将 Session 存储在 Redis 这种内存型数据库汇总，效率会更高。

使用 Session 维护用户登陆状态的过程如下：
* 用户进行登陆时，用户提交包含用户名和密码的表单，放入 HTTP 请求报文中；
* 服务器验证该用户名和密码，如果正确则把用户信息存储到 Redis 中，它在 Redis 中的 Key 称为 Session ID；
* 服务器返回的响应报文的 Set-Cookie 首部字段包含了这个 Session ID,客户端收到响应报文之后将该 Cookie 值存入浏览器中；
* 客户端之后对同一服务器进行请求时会包含该 Cookie 值，服务器收到之后提取出 Session ID，从 Redis 中取出用户信息，继续之后的业务操作。  

### 九、GET 和 POST 比较
















































































































