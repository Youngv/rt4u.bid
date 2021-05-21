---
title: Ubuntu 14.04 搭建 IPsec/L2TP VPN 服务器
---

对于在天朝的码农来说VPN是必不可少的利器，下面就介绍一下如何在 Ubuntu 14.04 上搭建一个 IPsec/L2TP VPN 服务器。

### 选择一个VPS

1. [Vultr](http://www.vultr.com/?ref=6857767) 按小时计费，灵活配置，最低$5/月的配置包含768M内存，1核CPU，20G的SSD硬盘和1000G的流量，推荐位置选择日本。
2. [DigitalOcean](https://m.do.co/c/2c9d1e6a3031) 按小时计费，灵活配置，最低$5/月的配置包含512M内存，1核CPU，20G的SSD硬盘和1000G的流量，推荐位置选择新加坡。

### 安装VPN服务器
1. 在服务器上下载脚本[hwdsl2/setup-ipsec-vpn](https://github.com/hwdsl2/setup-ipsec-vpn)

    `wget https://raw.githubusercontent.com/hwdsl2/setup-ipsec-vpn/master/vpnsetup.sh`

2. 执行脚本，脚本会安装`xl2tpd`和`ipsec`并配置`IPTABLES`，根据提示输入要设置的帐号，密码和IPsec密钥。

    `./vpnsetup.sh`

3. 一些命令和配置

    + 登录验证日志：`/var/log/auth.log`
    + xl2tpd工作日志：`/var/log/syslog`
    + xl2tpd配置文件：`/etc/xl2tpd/xl2tpd.conf`
    + ipsec配置文件：`/etc/ipsec.conf`
    + 重启服务：`/usr/sbin/service xl2tpd stop && /usr/sbin/service ipsec stop && /usr/sbin/service xl2tpd start && /usr/sbin/service ipsec start`

### Android 6.0 支持

1.修改`/etc/ipsec.conf`

```shell
# 修改以下两行配置，以此增加对sha2的支持
ike=3des-sha1,aes-sha1,aes256-sha1,aes256-sha2_256
phase2alg=3des-sha1,aes-sha1,aes256-sha1,aes256-sha2_256
# 新增一行配置
sha2-truncbug=yes
```

2.重启VPN服务器
