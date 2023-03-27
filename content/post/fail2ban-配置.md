---
title: "fail2ban 配置"
categories: ["后端"]
tags: ["Linux"]
draft: false
slug: "517"
date: "2018-04-08 21:53:00"
---

我们先要了解一些名词：
过滤器：过滤器定义了一个正则表达式，它必须匹配与登录失败或任何其他表达式相对应的模式
动作：一个动作定义了几个在不同时刻执行的命令
监狱：监狱是一个过滤器和一个或多个行动的组合。Fail2ban可以同时处理几个监狱。

### 配置
/etc/fail2ban目录如下：
```
/etc/fail2ban/
├── action.d
│   ├── dummy.conf
│   ├── hostsdeny.conf
│   ├── iptables.conf
│   ├── mail-whois.conf
│   ├── mail.conf
│   └── shorewall.conf
├── fail2ban.conf
├── fail2ban.local
├── filter.d
│   ├── apache-auth.conf
│   ├── apache-noscript.conf
│   ├── couriersmtp.conf
│   ├── postfix.conf
│   ├── proftpd.conf
│   ├── qmail.conf
│   ├── sasl.conf
│   ├── sshd.conf
│   └── vsftpd.conf
├── jail.conf
└── jail.local
```
每个.conf文件都可以用名为.local的文件覆盖。 .conf文件首先被读取，然后是.local，稍后的设置将覆盖较早的设置。 因此，.local文件不必在相应的.conf文件中包含所有内容，只包含那些您希望覆盖的设置。修改应该在.local中进行，而不是在.conf中进行。 这避免了升级时的合并问题。 这些文件都有详细的文档记录，并且应该有详细的信息。

#### Jails
```
[ssh-iptables]
#enabled  = false
enabled  = true
filter   = sshd
action   = iptables[name=SSH, port=ssh, protocol=tcp]
#          mail-whois[name=SSH, dest=yourmail@mail.com]
#logpath  = /var/log/sshd.log
logpath  = /var/log/auth.log
maxretry = 5
```
每个监狱只允许使用一个过滤器，但可以在单独的行上指定多个动作。 例如，您可以首先添加一个新的防火墙规则，然后使用whois检索有关违规主机的一些信息，最后发送电子邮件通知，从而对SSH闯入企图做出反应。

#### Filters
目录filter.d主要包含正则表达式，用于检测破解企图，密码失败等。下面是filter.d/sshd.conf的一个示例，其中包含3个可能的正则表达式以匹配日志文件的行：
```
failregex = Authentication failure for .* from <HOST>
            Failed [-/\w]+ for .* from <HOST>
            ROOT LOGIN REFUSED .* FROM <HOST>
            [iI](?:llegal|nvalid) user .* from <HOST>
```

#### Actions
目录action.d包含定义动作的不同脚本。 这些动作在执行Fail2ban期间的明确时刻执行：启动/停止监狱，禁止/解除主机等。


https://www.fail2ban.org/wiki/index.php/MANUAL_0_8#Configuration
