---
title: "【译】你需要了解有关HTTP安全标头的一切"
categories: ["前端"]
tags: ["前端安全"]
draft: false
slug: "241"
date: "2017-02-08 21:38:00"
---

一些物理学家28年前需要一种方法来轻松共享实验数据，因此网络诞生了。 这被普遍认为是一个创举。 不幸的是，一切物理学家接触到 - 从三角学到强大的核力量 - 最终变成武器化，其中也有超文本传输协议。

什么可以被攻击必须保卫，并且由于传统需要所有的安全特征是一个螺栓连接后的想法，事情...有点复杂。

本文介绍了什么是安全头，以及如何在Rails，Django，Express.js，Go，Nginx和Apache中实现这些头。

请注意，一些标头可能最好在您的HTTP服务器上配置，而其他标头应在应用程序层上设置。 在此处自行决定。 你可以测试你对Mozilla天文台的效果。

我们有什么不对吗？ 联系我们hello@appcanary.com。

#HTTP安全标头

## X-XSS-Protection

```
X-XSS-Protection: 0;
X-XSS-Protection: 1;
X-XSS-Protection: 1; mode=block

```

### Why?

跨站脚本，通常缩写XSS，是攻击者造成页面加载一些恶意javascript的攻击。 `X-XSS-Protection`是Chrome和Internet Explorer中的一项功能，旨在防止“反射”的XSS攻击 - 攻击者将恶意负载作为请求的一部分发送[1](https://blog.appcanary.com/2017/http-security-headers.html#fn1).

`X-XSS-Protection: 0` 关闭
`X-XSS-Protection: 1` 将过滤来自请求的脚本，但仍将呈现页面
`X-XSS-Protection: 1; mode=block` 当触发时，将阻止整个页面被呈现。

### Should I use it?

是的。设置 `X-XSS-Protection: 1; mode=block`。注意“过滤坏脚本”机制是有问题的；详细原因可以参考[这里](http://blog.innerht.ml/the-misunderstood-x-xss-protection/) 。

### How?

| Platform      | What do I do?                            |
| ------------- | ---------------------------------------- |
| Rails 4 and 5 | On by default                            |
| Django        | `SECURE_BROWSER_XSS_FILTER = True`       |
| Express.js    | Use [helmet](https://helmetjs.github.io/docs/xss-filter/) |
| Go            | Use [unrolled/secure](https://github.com/unrolled/secure) |
| Nginx         | `add_header X-XSS-Protection "1; mode=block";` |
| Apache        | `Header always set X-XSS-Protection "1; mode=block"` |

### I want to know more

[X-XSS-Protection - MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-XSS-Protection)

------

## Content Security Policy

```
Content-Security-Policy: <policy>

```

### Why?

内容安全策略可以看作是上面`X-XSS-Protection`的更高级版本。 虽然`X-XSS-Protection`将阻止来自请求的脚本，但它不会阻止涉及在您的服务器上存储恶意脚本或加载外部资源的恶意脚本的XSS攻击。

CSP为您提供了一种语言来定义浏览器可以从中加载资源的位置。您可以以非常精细的方式列出脚本，图像，字体，样式表等的原始列表。您还可以将任何加载的内容与散列或签名进行比较。

### Should I use it?

是。 它不会阻止所有XSS攻击，但它能显着减轻它们的影响，并且是深度防御的一个重要方面。 也就是说，它可能很难实现。 如果你是一个勇敢的读者，继续并检查appcanary.com 返回的headers[2](https://blog.appcanary.com/2017/http-security-headers.html#fn2)，你会看到我们没有CSP实现。 因为我们正在使用一些rails开发的插件，它阻碍我们设置CSP，所以可能会有实际的安全影响。 我们正在努力，并将在下一期实现！

### How?

编写CSP策略可能具有挑战性。请参阅 [此处](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy)查看您可以使用的所有指令的列表。
 [这里](https://csp.withgoogle.com/docs/adopting-csp.html)有一个很好的入门示例。

| Platform      | What do I do?                            |
| ------------- | ---------------------------------------- |
| Rails 4 and 5 | Use [secureheaders](https://github.com/twitter/secureheaders) |
| Django        | Use [django-csp](https://github.com/mozilla/django-csp) |
| Express.js    | Use [helmet/csp](https://github.com/helmetjs/csp) |
| Go            | Use [unrolled/secure](https://github.com/unrolled/secure) |
| Nginx         | `add_header Content-Security-Policy "";` |
| Apache        | `Header always set Content-Security-Policy ""` |

### I want to know more

- [Content-Security-Policy - MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy)
- [CSP Quick Reference Guide](https://content-security-policy.com/)
- [Google’s CSP Guide](https://csp.withgoogle.com/docs/index.html)

------

## HTTP Strict Transport Security (HSTS)

```
Strict-Transport-Security: max-age=<expire-time>
Strict-Transport-Security: max-age=<expire-time>; includeSubDomains
Strict-Transport-Security: max-age=<expire-time>; preload

```

### Why?

当我们想与某人安全地沟通时，我们面临两个问题。第一个问题是隐私；我们要确保我们发送的邮件只能由收件人读取，并且没有其他人。另一个问题是认证：难道收件人说她是谁就是谁吗？

HTTPS解决了加密的第一个问题，尽管它在身份验证方面有一些主要问题（稍后详细介绍，请参阅[公钥锁定](https://blog.appcanary.com/2017/http-security-headers.html#http-public-key-pinning-hpkp)）。 HSTS头解决了元问题：你怎么知道你说的人实际上是否支持加密？

HSTS减少了名为 [sslstrip](https://moxie.org/software/sslstrip/)的攻击。假设您使用的是恶意攻击者控制的WiFi。攻击者可以禁用您和您正在浏览的网站之间的加密。即使您正在访问的网站只能通过HTTPS，攻击者可以中间人的HTTP流量，使它看起来像该网站工作在未加密的HTTP。不需要SSL证书，只需禁用加密。

输入HSTS。 `Strict-Transport-Security`头通过让浏览器知道它必须始终对您的网站使用加密来解决此问题。只要您的浏览器已经看到一个HSTS标题 - 它没有过期，它将不会访问未加密的网站，如果它通过HTTP访问就会报错。

### Should I use it?

是。 您的应用只能HTTPS使用，对吗？ 尝试通过HTTP访问将被重定向到安全网站，对吧？ （提示：如果你想避免商业证书颁发机构的证书，请使用 [letsencrypt](https://letsencrypt.org/)）

HSTS的一个缺点是，它允许一个[聪明的技术](http://www.radicalresearch.co.uk/lab/hstssupercookies)来创建supercookies，它可以获得用户的指纹。 作为网站运营商，您可能已经在某种程度上跟踪您的用户，但尝试仅使用HSTS来获得好处，而不是supercookies。

### How?

有两个选项

- `includeSubDomains` - HSTS适用子域
- `preload` - Google会维护一项将您的网站硬编码[3](https://blog.appcanary.com/2017/http-security-headers.html#fn3)为仅在浏览器中使用HTTPS的[服务](https://hstspreload.appspot.com/) 。 这样，用户甚至不必访问您的网站：他们的浏览器已经知道它应该拒绝未加密的连接。 顺便说一句，清除列表是很困难的，所以只有当你知道你可以支持HTTPS永远在所有的子域名时才打开它。

| Platform   | What do I do?                            |
| ---------- | ---------------------------------------- |
| Rails 4    | `config.force_ssl = true`Does not include subdomains by default. To set it:`config.ssl_options = { hsts: { subdomains: true } }` |
| Rails 5    | `config.force_ssl = true`                |
| Django     | `SECURE_HSTS_SECONDS = 31536000` `SECURE_HSTS_INCLUDE_SUBDOMAINS = True` |
| Express.js | Use [helmet](https://helmetjs.github.io/docs/hsts/) |
| Go         | Use [unrolled/secure](https://github.com/unrolled/secure) |
| Nginx      | `add_header Strict-Transport-Security "max-age=31536000; includeSubdomains; ";` |
| Apache     | `Header always set Strict-Transport-Security "max-age=31536000; includeSubdomains;` |

### I want to know more

- [RFC 6797](https://tools.ietf.org/html/rfc6797)
- [Strict-Transport-Security - MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security)

------

## HTTP Public Key Pinning (HPKP)

```
Public-Key-Pins: pin-sha256=<base64==>; max-age=<expireTime>;
Public-Key-Pins: pin-sha256=<base64==>; max-age=<expireTime>; includeSubDomains
Public-Key-Pins: pin-sha256=<base64==>; max-age=<expireTime>; report-uri=<reportURI>

```

### Why?

上述HSTS标题旨在确保与您网站的所有连接都已加密。然而， nowhere does it specify what key to use!

Web上的信任基于证书颁发机构（CA）模型。您的浏览器和操作系统随附一些受信任的证书颁发机构的公钥，这些证书颁发机构通常是专门公司和/或国家/地区。当CA向您颁发给定域的证书时，意味着任何人信任该CA将自动信任您使用该证书加密的SSL流量。 CA负责验证您实际拥有一个域（这可以是发送电子邮件，请求您托管文件，调查您的公司）。

两个CA可以向两个不同的人颁发同一域的证书，浏览器将信任这两个CA。这产生了一个问题，特别是因为CA可以[伪造](https://technet.microsoft.com/library/security/2524375)和被攻击。这允许攻击者MiTM任何他们想要的域，即使该域使用SSL和HSTS！

HPKP头试图缓解这一点。此标题允许您“固定”证书。当浏览器第一次看到HPKP头时，它将保存证书。对于每个请求直到 `max-age`，浏览器将失败，除非从服务器发送的链中至少有一个证书具有固定的指纹。

这意味着你可以固定到CA或与叶子一起的中间证书，以便防止搬起石头砸了自己的脚 （下面会解释）。

就像上面的HSTS，HPKP头也有一些隐私影响。这些都是因为 [RFC](https://tools.ietf.org/html/rfc7469#section-5)本身。

### Should I use it?

可能不会。

HPKP是一个非常非常锋利的刀。 考虑这个：如果你固定错误的证书，或者你失去了你的钥匙，或者其他的东西出了问题，你已经锁定你的用户离开你的网站。 你所能做的只是等待“固定”过期。

这篇 [文章](https://blog.qualys.com/ssllabs/2016/09/06/is-http-public-key-pinning-dead)阐述了针对它的案例，并包括一个有趣的方式，攻击者使用HPKP来保存他们的受害者赎金。

一个替代方法是使用`Public-Key-Pins-Report-Only`，它只是报告出了问题，但不锁定任何人。 这允许你至少知道你的用户正在使用假证书。

### How?

它有两个选项

- `includeSubDomains` - HPKP适用子域
- `report-uri` - 将在此处报告无效尝试

您必须为您固定的密钥生成base64编码指纹，您**必须**使用备份密钥。请查看[这里](https://developer.mozilla.org/en-US/docs/Web/HTTP/Public_Key_Pinning#Extracting_the_Base64_encoded_public_key_information) 以了解如何操作。

| Platform      | What do I do?                            |
| ------------- | ---------------------------------------- |
| Rails 4 and 5 | Use [secureheaders](https://github.com/twitter/secureheaders/blob/master/docs/HPKP.md) |
| Django        | Write custom middleware                  |
| Express.js    | Use [helmet](https://helmetjs.github.io/docs/hpkp/) |
| Go            | Use [unrolled/secure](https://github.com/unrolled/secure) |
| Nginx         | `add_header Public-Key-Pins 'pin-sha256=""; pin-sha256=""; max-age=5184000; includeSubDomains';` |
| Apache        | `Header always set Public-Key-Pins 'pin-sha256=""; pin-sha256=""; max-age=5184000; includeSubDomains';` |

### I want to know more

- [RFC 7469](https://tools.ietf.org/html/rfc7469)
- [HTTP Public Key Pinning (HPKP) - MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Public_Key_Pinning)

------

## X-Frame-Options

```
X-Frame-Options: DENY
X-Frame-Options: SAMEORIGIN
X-Frame-Options: ALLOW-FROM https://example.com/

```

### Why?

这个想法是这样的：你创建一个不可见的iframe，将它放在焦点并将用户输入路由到它。 作为攻击者，你可以诱骗人们玩一个基于浏览器的游戏，而他们的点击是由隐藏的iframe显示twitter注册 - 迫使他们非自愿转发所有的tweets。

这听起来很蠢，但它是一个有效的攻击。

### Should I use it?


是。你的应用程序是一个美丽的雪花。你能想象到一些[天才](https://techcrunch.com/2015/04/08/annotate-this/)会把它放到iframe，然后他们可以破坏它吗？

### How?

`X-Frame-Options` 有三种模式，顾名思义。

- `DENY` - 没有人可以将此网页放在iframe中
- `SAMEORIGIN` - 该页面只能由同一来源的某个iframe显示。
- `ALLOW-FROM` - 指定可将网页放在iframe中的特定网址


需要记住的一件事是，你可以堆叠iframe想多深就多深，在这种情况下，`SAMEORIGIN` 和 `ALLOW-FROM`的行为没有[指定](https://tools.ietf.org/html/rfc7034#section-2.3.2.2).。 也就是说，如果我们有一个三层的iframe三明治，最内层的iframe有`SAMEORIGIN`，我们关心它周围的iframe的起源，还是页面上最上面的iframe？ ¯\ _（ツ）_ /。	

| Platform      | What do I do?                            |
| ------------- | ---------------------------------------- |
| Rails 4 and 5 | `SAMEORIGIN` is set by default.To set `DENY`:`config.action_dispatch.default_headers['X-Frame-Options'] = "DENY"` |
| Django        | `MIDDLEWARE = [ ... 'django.middleware.clickjacking.XFrameOptionsMiddleware', ... ]`This defaults to `SAMORIGIN`.To set `DENY`: `X_FRAME_OPTIONS = 'DENY'` |
| Express.js    | Use [helmet](https://helmetjs.github.io/docs/frameguard/) |
| Go            | Use [unrolled/secure](https://github.com/unrolled/secure) |
| Nginx         | `add_header X-Frame-Options "deny";`     |
| Apache        | `Header always set X-Frame-Options "deny"` |

### I want to know more

- [RFC 7034](https://tools.ietf.org/html/rfc7034)
- [X-Frame-Options - MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options)

------

## X-Content-Type-Options

```
X-Content-Type-Options: nosniff;

```

### Why?

MIME嗅探”，这实际上是一个浏览器的“功能”。

在理论上，每当你的服务器响应一个请求，它应该设置一个`Content-Type` 头，以告诉浏览器它是否得到一些HTML还是一个gif图或2008年的Flash动画。不幸的是，web标准一直被打破，从来没有真正遵循规范的任何东西；回到当天很多人没有打扰正确设置内容类型头。

因此，浏览器供应商决定他们应该真正有帮助，并尝试通过检查内容本身，而完全忽略内容类型标题推断内容类型。如果它看起来像一个gif，显示一个gif！，即使内容类型是`text/html`。同样，如果它看起来像我们得到一些HTML，我们应该渲染它，即使服务器说它是一个gif。

这是伟大的，除了当你运行照片共享网站，用户可以上传照片，看起来像HTML包含JavaScript，突然你有一个存储的XSS攻击你的手。

`X-Content-Type-Options`告诉浏览器关闭和设置该死的内容类型，谢谢你。

### Should I use it?

是的，只要确保正确设置您的内容类型。

### How?

| Platform      | What do I do?                            |
| ------------- | ---------------------------------------- |
| Rails 4 and 5 | On by default                            |
| Django        | `SECURE_CONTENT_TYPE_NOSNIFF = True`     |
| Express.js    | Use [helmet](https://helmetjs.github.io/docs/dont-sniff-mimetype/) |
| Go            | Use [unrolled/secure](https://github.com/unrolled/secure) |
| Nginx         | `add_header X-Content-Type-Options nosniff;` |
| Apache        | `Header always set X-Content-Type-Options nosniff` |

### I want to know more

- [X-Content-Type-Options - MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Content-Type-Options)

------

## Referrer-Policy

```
Referrer-Policy: "no-referrer" 
Referrer-Policy: "no-referrer-when-downgrade" 
Referrer-Policy: "origin" 
Referrer-Policy: "origin-when-cross-origin"
Referrer-Policy: "same-origin" 
Referrer-Policy: "strict-origin" 
Referrer-Policy: "strict-origin-when-cross-origin" 
Referrer-Policy: "unsafe-url"
```

### Why?

The `Referrer-Policy` header allows you to specify when the browser will set a `Referer`header.
啊，`Referer`头。非常适合分析，不利于您的用户的隐私。 在某些时候，比如唤醒网络，并决定发送它也许不总是一个好主意。 虽然我们在这里，让我们正确拼写“Referrer”[4](https://blog.appcanary.com/2017/http-security-headers.html#fn4)。

`Referrer-Policy`头允许您指定浏览器何时设置Referer头。

### Should I use it?

这取决于你，但它可能是一个好主意。 如果你不关心你的用户的隐私，你可以把它看作是一种保持甜蜜蜜分析的方式，远离你的竞争对手的肮脏的手。

设置 `Referrer-Policy: "no-referrer"`

### How?

| Platform      | What do I do?                            |
| ------------- | ---------------------------------------- |
| Rails 4 and 5 | Use [secureheaders](https://github.com/twitter/secureheaders) |
| Django        | Write custom middleware                  |
| Express.js    | Use [helmet](https://helmetjs.github.io/docs/referrer-policy/) |
| Go            | Write custom middleware                  |
| Nginx         | `add_header Referrer-Policy "no-referrer";` |
| Apache        | `Header always set Referrer-Policy "no-referrer"` |

### I want to know more

- [Referrer Policy - MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referrer-Policy)

------

## Cookie Options

```
Set-Cookie: <key>=<value>; Expires=<expiryDate>; Secure; HttpOnly; SameSite=strict

```

### Why?

这起初不是一个安全头，但有三个不同的选项，你应该知道的cookie。

- 标记为`Secure` 的Cookie只能通过HTTPS投放。这可以防止有人在MiTM攻击中读取Cookie，他们可以强制浏览器访问给定的页面。



- `HttpOnly`是一个misnomer，并与HTTPS无关（不同于上面的`Secure` ）。标记为`HttpOnly`的Cookie无法从javascript中访问。所以如果有一个XSS缺陷，攻击者不能立即窃取cookie。



- `SameSite`帮助防御跨源请求伪造（CSRF）攻击。这是一种攻击，其中用户可能正在访问的不同网站无意中欺骗他们对您的网站发出请求，即通过包括用于发出GET请求的图像，或者使用javascript来提交用于POST请求的表单。一般来说，人们使用CSRF令牌来防御这种情况。标记为`SameSite`的Cookie不会发送到其他网站。


它有两种模式，lax和strict。 Lax模式允许在GET请求的顶级上下文中发送Cookie（即，如果您单击了链接）。 Strict不发送任何第三方Cookie。

### Should I use it?

你绝对应该设置`Secure`和`HttpOnly`。 不幸的是，在写作时，SameSite Cookie仅在Chrome和Opera中[可用](http://caniuse.com/#search=samesite) ，因此目前您可能想要忽略它们。

### How?

| Platform      | What do I do?                            |
| ------------- | ---------------------------------------- |
| Rails 4 and 5 | Secure and HttpOnly enabled by default. For SameSite, use [secureheaders](https://github.com/twitter/secureheaders) |
| Django        | Session cookies are HttpOnly by default. To set secure: `SESSION_COOKIE_SECURE = True`. Not sure about SameSite. |
| Express.js    | `cookie: { secure: true, httpOnly: true, sameSite: true }` |
| Go            | `http.Cookie{Name: "foo", Value: "bar", HttpOnly: true, Secure: true}` For SameSite, see this [issue](https://github.com/golang/go/issues/15867). |
| Nginx         | You probably won’t set session cookies in Nginx |
| Apache        | You probably won’t set session cookies in Apache |

### I want to know more

- [Cookies - MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#Secure_and_HttpOnly_cookies)

------

Thanks to [@wolever](https://twitter.com/wolever) for python advice

------

1. This is opposed to “stored” XSS attacks, where the attacker is storing the malicious payload somehow, i.e. in a vulnerable comment field of a message board. [↩](https://blog.appcanary.com/2017/http-security-headers.html#fnref1)
2. If you’re going to point out in the Hacker News comments that this blog itself gets an F from the Mozilla observatory, you’re right! On the other hand, it’s serving static content, and we are comfortable avoiding XSS protection and strict SSL enforcement for static content. That, and it’s served by github pages/cloudflare, so it’s hard to get very granular about the headers we want set. [↩](https://blog.appcanary.com/2017/http-security-headers.html#fnref2)
3. So if you’re especially paranoid, you might be thinking “what if I had some secret subdomain that I don’t want leaking for some reason?” You have DNS zone transfers disabled, so someone would have to know what they’re looking for to find it, but now that it’s in the preload list… [↩](https://blog.appcanary.com/2017/http-security-headers.html#fnref3)
4. The `Referer` header is the [Hampster Dance](http://www.hamsterdance.org/hamsterdance/) in that it’s notorious for being misspelled. It would break the web to try to backport the correct spelling, so instead the W3C decided to go for the worst of both worlds and spell it correctly in `Referrer-Policy`. [↩](https://blog.appcanary.com/2017/http-security-headers.html#fnref4)
