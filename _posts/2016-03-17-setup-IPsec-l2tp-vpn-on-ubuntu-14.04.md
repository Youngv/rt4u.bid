---
layout: post
title: Ubuntu 14.04 搭建 IPsec/L2TP VPN 服务器
---

## 概述

对于在天朝的码农来说 VPN 是必不可少的利器，下面就介绍一下如何在 Ubuntu 14.04 上搭建一个 IPsec/L2TP VPN 服务器。IPsec/L2TP 协议是目前各大平台（Windows、Mac、iOS、Android）都原生支持的 VPN 协议，无需安装额外的客户端软件。

## 选择合适的 VPS

选择一个稳定、速度快的 VPS 是搭建 VPN 的第一步。以下是两个推荐的 VPS 提供商：

1. [Vultr](http://www.vultr.com/?ref=6857767) 
   - 按小时计费，灵活配置
   - 最低$5/月的配置包含 768M 内存，1 核 CPU，20G 的 SSD 硬盘和 1000G 的流量
   - 推荐位置选择日本，延迟较低

2. [DigitalOcean](https://m.do.co/c/2c9d1e6a3031) 
   - 按小时计费，灵活配置
   - 最低$5/月的配置包含 512M 内存，1 核 CPU，20G 的 SSD 硬盘和 1000G 的流量
   - 推荐位置选择新加坡，连接稳定

## 安装 VPN 服务器

### 下载并运行安装脚本

1. 在服务器上下载脚本 [hwdsl2/setup-ipsec-vpn](https://github.com/hwdsl2/setup-ipsec-vpn)

   ```bash
   wget https://raw.githubusercontent.com/hwdsl2/setup-ipsec-vpn/master/vpnsetup.sh
   ```

2. 给脚本添加执行权限并运行

   ```bash
   chmod +x vpnsetup.sh
   ./vpnsetup.sh
   ```

   脚本会安装`xl2tpd`和`ipsec`并配置`IPTABLES`，根据提示输入要设置的帐号，密码和 IPsec 密钥。

### 重要文件和命令

以下是一些重要的文件路径和常用命令，便于后续管理和故障排查：

- **日志文件**：
  - 登录验证日志：`/var/log/auth.log`
  - xl2tpd 工作日志：`/var/log/syslog`

- **配置文件**：
  - xl2tpd 配置文件：`/etc/xl2tpd/xl2tpd.conf`
  - ipsec 配置文件：`/etc/ipsec.conf`

- **重启服务**：

  ```bash
  /usr/sbin/service xl2tpd stop && /usr/sbin/service ipsec stop && /usr/sbin/service xl2tpd start && /usr/sbin/service ipsec start
  ```

## Android 6.0 及更高版本支持

Android 6.0 及更高版本需要额外的配置才能正常连接 IPsec/L2TP VPN。

### 修改配置文件

1. 编辑 ipsec 配置文件，增加对 sha2 的支持

   ```bash
   sudo nano /etc/ipsec.conf
   ```

2. 修改以下配置项

   ```
   # 修改以下两行配置，以此增加对 sha2 的支持
   ike=3des-sha1,aes-sha1,aes256-sha1,aes256-sha2_256
   phase2alg=3des-sha1,aes-sha1,aes256-sha1,aes256-sha2_256
   
   # 新增一行配置
   sha2-truncbug=yes
   ```

3. 保存并退出编辑器（在 nano 中按 Ctrl+O 保存，Ctrl+X 退出）

### 重启 VPN 服务

修改配置后需要重启 VPN 服务使配置生效：

```bash
sudo service xl2tpd restart
sudo service ipsec restart
```

## 客户端连接

各平台连接 IPsec/L2TP VPN 的方法：

### Windows

1. 打开"网络和共享中心" → "设置新的连接或网络" → "连接到工作区"
2. 选择"使用我的 Internet 连接 (VPN)"
3. 输入服务器地址
4. 在"连接属性"→"安全"中，VPN 类型选择"L2TP/IPsec"
5. 点击"高级设置"，选中"使用预共享密钥"，输入安装时设置的 IPsec 密钥

### macOS/iOS

1. 打开"系统偏好设置"→"网络"→点击"+"添加新服务
2. 接口选择"VPN"，VPN 类型选择"L2TP over IPSec"
3. 输入服务名称、服务器地址、账户名称
4. 点击"认证设置"，输入用户密码和共享密钥 (IPsec 密钥)

### Android

1. 打开"设置"→"网络和 Internet"→"VPN"→点击"+"
2. 输入名称，选择类型为"L2TP/IPsec PSK"
3. 输入服务器地址、IPsec 预共享密钥
4. 保存后点击连接，输入用户名和密码
