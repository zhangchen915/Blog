---
title: "Let's Encrypt 实现全站 HTTPS"
categories: ["后端"]
tags: ["HTTPS","nginx"]
draft: false
slug: "293"
date: "2017-04-17 23:03:00"
---

# 获取证书
访问 [Certbot][1] 获取相应安装命令。（Certbot官方的自动化获取、部署和更新安全证书的工具）
安装完Certbot后执行`certbot certonly`

之后会提示如下内容：
```
How would you like to authenticate with the ACME CA?
-------------------------------------------------------------------------------
1: Place files in webroot directory (webroot)
2: Spin up a temporary webserver (standalone)
-------------------------------------------------------------------------------
```
webroot 模式会要你输入网站根目录（例如 /var/www/example）然后会在其中创建 .well-known 文件夹，这个文件夹里面包含了一些验证文件，certbot 会通过访问 example.com/.well-known/acme-challenge 来验证你的域名是否绑定的这个服务器。

standalone 模式会要你输入域名（例如 vps.zhangchen915.com），然后程序会启动服务器的443端口，来验证域名的归属（注意验证时443端口不能被其它服务占用，如 Nginx）。
　
个人感觉第二种比较方便。

验证成功后，会生成四个证书文件`cert.pem  chain.pem  fullchain.pem  privkey.pem`，他们会被放在`/etc/letsencrypt/live/`目录下对应的域名文件夹中。

# 配置 nginx
如果你不太熟悉nginx，推荐访问[Mozilla SSL Configuration Generator][2]生成配置文件。
这里生成的配置文件应还会有两部分（server{...}是一部分），第一部分的作用是将80端口的HTTP服务301重定向到对应的HTTPS地址，第二部分是配置SSL连接。

nginx 的默认配置在 `/etc/nginx/conf.d/`文件夹下。如果你之前有HTTP服务的话，你应该修改过`default.conf`，如一些反向代理，备份一份，然后用第一部分替换掉。

然后在`ssl.conf`中写入第二部分的配置
```
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;

    # certs sent to the client in SERVER HELLO are concatenated in ssl_certificate
     ssl_certificate /etc/letsencrypt/live/<你的域名>/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/<你的域名>/privkey.pem;
    ssl_session_timeout 1d;
    ssl_session_cache shared:SSL:50m;
    ssl_session_tickets off;

    ....

    # 注意：Let's Encrypt 签发的证书, 可以忽略ssl_stapling_verify 和 ssl_trusted_certificate 两个配置
    ## verify chain of trust of OCSP response using Root CA and Intermediate certs
    # ssl_trusted_certificate /path/to/root_CA_cert_plus_intermediates;

    # resolver <IP DNS resolver>;

    <之前在default.conf的配置可以放在这里>
}
```

最后，让nginx重新载入配置文件`service nginx reload`就能看到效果了。

# 单IP配置多个https server

> 在单个IP地址上运行多个HTTPS服务器的更通用的解决方案是TLS服务器名称指示扩展（SNI，RFC 6066），允许浏览器在SSL握手期间传递所请求的服务器名称，因此服务器将知道链接用哪个证书。

但是SNI需要服务端和浏览器都支持。

- 服务端
 运行`nginx -V`，如果返回信息里包括`TLS SNI support enabled`，则表明OpenSSL支持SNI。

- 浏览器
 IE 7及以上版本（注意：在XP系统中任何版本的IE浏览器都不支持SNI）

nginx的配置也很简单，在对应的配置文件加入`server_name www.example.org;`即可。

# 完全向前保密

> 完全向前保密 (PFS) 是一个 IPSec 属性，用于确保其中一个专用密钥在今后发生泄漏时，派生的会话密钥不会泄漏。
为了防止第三方发现密钥值，IPSec 使用完全向前保密 (PFS)。 PFS 将根据双方在交换时提供的值定期创建新的密钥值。 由于双方都提供了一个只有他们知道的随机值，因此生成的每个新密钥不同于先前创建的密钥。
使用 PFS 表示，即使第三方设法拦截对称密钥，也只能短时间地使用拦截的密钥。 此外，由于新创建的密钥并非基于先前拦截的密钥，因此第三方必须开始新的暴力计算以猜测新的密钥值。 在 IPSec 中启用了 PFS 的情况下，创建新密钥花费的时间长于未使用 PFS 的情况。 但是，使用 PFS 有助于阻止第三方拦截数据和对数据进行解码。

PFS 使用了『迪菲－赫尔曼加密法』，所以需要一些额外的配置。
1、需要生成一个 dhparam.pem 文件。这个文件在 DH 握手时会跟传给客户端。这是“DHE加密法”需要用上的东西。
2、告诉 Nginx 使用不要使用默认的加密算法。

我们执行如下命令：
`openssl dhparam -out /etc/letsencrypt/live/<你的域名>/dhparam.pem 2048` 
生成完成后在nginx加入如下配置：`
```
# Diffie-Hellman parameter for DHE ciphersuites, recommended 2048 bits
    ssl_dhparam /etc/letsencrypt/live/<你的域名>/dhparam.pem;
```
 
# 续签
证书默认90天过期，但是我们可以执行更新操作告诉 Let's Encrypt 我们机器还活着。这件事情可以直接交给`crontab`定时任务来完成。crontab在ubuntu中服务名为cron。下面这段内容表示每隔1个月的凌晨 2:30执行更新操作，更多关于crontab命令相关内容可以参考[这里][3]。

```bash
30 2 * */1 * certbot renew --pre-hook "service nginx stop" --post-hook "service nginx start"
```

参考文献：
[Configuring HTTPS servers][4]
[完全向前保密][5]
[Nginx 开启 https 双证书指南][6]


  [1]: https://certbot.eff.org/
  [2]: https://mozilla.github.io/server-side-tls/ssl-config-generator/
  [3]: http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html
  [4]: http://nginx.org/en/docs/http/configuring_https_servers.html
  [5]: https://www.ibm.com/support/knowledgecenter/zh/SSETBF_3.1.1/com.ibm.siteprotector.doc/references/sp_agenthelp_perfect_forward_secrecy.htm
  [6]: https://www.zeroling.com/nginx-kai-qi-https-shuang-zheng-shu-zhi-nan/
