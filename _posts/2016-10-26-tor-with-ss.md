---
layout: post
title: Tor 和 Shadowsocks 双重高度匿名代理
---

## 概述

互联网上存在着大量的监控、审查和流量分析，为了保护个人隐私和数据安全，有时我们需要更高级别的匿名性。本文介绍如何结合使用 Tor 和 Shadowsocks 两种工具，构建双重代理，实现更高度的匿名上网。

## 技术背景

### Tor 网络简介

Tor（The Onion Router，洋葱路由器）是一个著名的隐私网络，用于防范互联网上广泛存在的流量分析和监控。Tor 的主要特点包括：

- **洋葱路由技术**：数据经过至少三个节点的多层加密传输
- **去中心化网络**：由全球志愿者运行的数千个中继节点构成
- **匿名性**：有效隐藏用户的真实 IP 地址和物理位置
- **支持隐藏服务**：可以匿名托管网站和服务（.onion 域名）

Tor 适用于需要高度匿名的场景，如记者保护消息来源、政治活动家规避审查、企业保护敏感通信等。

### Shadowsocks 简介

Shadowsocks（中文名称：影梭）是一个轻量级的代理工具，具有以下特点：

- **基于 SOCKS5 协议**：提供高效的数据传输
- **强加密**：支持多种加密方式保护数据传输
- **开源软件**：使用 Python、C++、C# 等语言开发，基于 Apache 许可证
- **低延迟**：相比 Tor 网络，通常具有更好的连接速度
- **简单易用**：客户端支持多平台（Windows、macOS、Linux、Android、iOS）

## 双重代理的工作原理

本文描述的设置采用 Shadowsocks -> Tor 的链式代理结构：

1. 本地应用程序 -> Tor SOCKS5 代理（127.0.0.1:9050）
2. Tor -> Shadowsocks SOCKS5 代理（127.0.0.1:1080）
3. Shadowsocks -> 互联网

这种配置的优势：
- 避免 ISP 或本地网络检测到 Tor 流量
- 规避对 Tor 入口节点的封锁
- 提供双重加密和匿名保护
- 增加追踪难度，提高安全性

## 安装配置指南

### 安装 Tor

在 macOS 上使用 Homebrew 安装 Tor：

```bash
brew install tor
```

在 Linux（Debian/Ubuntu）上安装：

```bash
sudo apt update
sudo apt install tor
```

### 安装 Shadowsocks

macOS 使用 Homebrew 安装：

```bash
brew install shadowsocks-libev
```

或使用图形界面客户端：[ShadowsocksX-NG](https://github.com/shadowsocks/ShadowsocksX-NG/releases)

### 配置 Shadowsocks

1. 确保你已有可用的 Shadowsocks 服务器
2. 配置 Shadowsocks 客户端连接到服务器
3. 确认 Shadowsocks 本地监听端口为 1080（标准配置）

### 配置 Tor

1. 编辑 Tor 配置文件：

```bash
sudo nano /usr/local/etc/tor/torrc
# 或
sudo vim /usr/local/etc/tor/torrc
```

2. 添加或修改以下配置：

```
# 通过 Shadowsocks 代理连接到 Tor 网络
Socks5Proxy 127.0.0.1:1080

# 排除可能的蜜罐节点或不安全节点
ExcludeNodes {cn},{hk},{mo},{kp},{ir},{sy},{pk},{cu},{vn}

# 严格遵循节点排除规则
StrictNodes 1

# 其他可选配置
# 控制连接超时时间
CircuitBuildTimeout 60

# 自动重新生成新的 Tor 线路（秒）
NewCircuitPeriod 1800
```

### 启动服务

1. 首先启动 Shadowsocks：
   - 如果使用命令行客户端：`ss-local -c /path/to/config.json`
   - 如果使用 GUI 客户端：直接启动应用程序

2. 启动 Tor：

```bash
tor
```

成功启动后，您将看到类似以下输出：

```
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

## 使用双重代理

### 浏览器配置

1. **Firefox**：
   - 打开设置 -> 网络设置
   - 选择"手动配置代理"
   - SOCKS主机：127.0.0.1，端口：9050
   - 选择"SOCKS v5"
   - 勾选"通过代理服务器发送DNS请求"

2. **Chrome/Safari**：
   - 安装 SwitchyOmega 插件
   - 创建 SOCKS5 代理配置
   - 地址：127.0.0.1，端口：9050

### 验证配置

访问以下网站检测是否成功使用 Tor 网络：
- [Tor 项目官方检测](https://check.torproject.org/)
- [IP 泄漏测试](https://ipleak.net/)

## 安全注意事项

1. **浏览器指纹**：
   - 使用 Tor 浏览器而非普通浏览器可以更好地防止浏览器指纹识别
   - 避免安装过多扩展和插件，它们可能泄露身份信息

2. **配置安全**：
   - 定期更新 Tor 和 Shadowsocks 客户端到最新版本
   - 避免同时在 Tor 网络内外进行身份相关的活动

3. **使用限制**：
   - 由于双重代理，网络连接速度会显著降低
   - 不适合大文件下载或流媒体观看等高带宽场景
   - 一些网站可能会阻止来自 Tor 出口节点的流量

## 高级配置选项

### 透明代理

如果想让所有系统流量都通过 Tor 网络，可以配置透明代理：

```
# 在 torrc 文件中添加
TransPort 9040
DNSPort 5353
```

然后配置系统防火墙将所有流量重定向到 Tor。

### 网桥和混淆

如果你所在的网络阻止了直接连接到 Tor 网络，可以使用网桥和混淆插件：

```
# 在 torrc 文件中添加
UseBridges 1
ClientTransportPlugin obfs4 exec /usr/local/bin/obfs4proxy
Bridge obfs4 X.X.X.X:XXXX FINGERPRINT
```

## 总结

Tor 和 Shadowsocks 的组合使用可以提供高度的匿名性和安全性，适合在需要保护隐私的场景下使用。但需要注意，没有系统是完全安全的，使用这些工具时仍需保持安全意识，采取额外的安全措施保护个人隐私和数据安全。
