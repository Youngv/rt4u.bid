---
title: Tor 和 Shadowsocks 双重高度匿名代理
---

#### Tor

Tor 用于防范互联网上广泛存在的流量过滤、嗅探分析。Tor 在由“洋葱路由”组成的表层网（overlay network）上进行通信，可以实现匿名对外连接、匿名隐藏服务。

#### Shadowsocks

Shadowsocks（中文名称：影梭）是使用 Python、C++、C# 等语言开发的、基于 Apache 许可证的开放源代码软件，用于保护网络流量、加密数据传输。

#### 安装

```shell
$ brew install tor
```

#### 配置文件：
+ /usr/local/etc/tor/torrc

#### 修改配置文件：
+ Socks5Proxy 127.0.0.1:1080 #通过 Shadowsocks 代理连接
+ ExcludeNodes {cn},{hk},{mo},{kp},{ir},{sy},{pk},{cu},{vn} #屏蔽蜜罐节点
+ strictnodes 1 # 严格节点模式

#### 启动 Tor：
+ tor

```
$ tor
Nov 21 11:38:05.253 [notice] Tor v0.2.8.6 running on Darwin with Libevent 2.0.22-stable, OpenSSL 1.0.2h and Zlib 1.2.8.
Nov 21 11:38:05.253 [notice] Tor can't help you if you use it wrong! Learn how to be safe at https://www.torproject.org/download/download#warning
Nov 21 11:38:05.253 [notice] Read configuration file "/usr/local/etc/tor/torrc".
Nov 21 11:38:05.257 [notice] Opening Socks listener on 127.0.0.1:9050
Nov 21 11:38:05.000 [notice] Parsing GEOIP IPv4 file /usr/local/Cellar/tor/0.2.8.6/share/tor/geoip.
Nov 21 11:38:05.000 [notice] Parsing GEOIP IPv6 file /usr/local/Cellar/tor/0.2.8.6/share/tor/geoip6.
Nov 21 11:38:05.000 [notice] Bootstrapped 0%: Starting
Nov 21 11:38:05.000 [notice] Bootstrapped 45%: Asking for relay descriptors
Nov 21 11:38:09.000 [notice] Bootstrapped 73%: Loading relay descriptors
Nov 21 11:38:11.000 [notice] Bootstrapped 80%: Connecting to the Tor network
Nov 21 11:38:11.000 [notice] Bootstrapped 90%: Establishing a Tor circuit
Nov 21 11:38:16.000 [notice] Tor has successfully opened a circuit. Looks like client functionality is working.
Nov 21 11:38:16.000 [notice] Bootstrapped 100%: Done
```

#### 设置浏览器代理：
+ 127.0.0.1:9050

#### 检测是否通过 Tor 代理访问网络
+ [Tor 代理检测](https://check.torproject.org/)
