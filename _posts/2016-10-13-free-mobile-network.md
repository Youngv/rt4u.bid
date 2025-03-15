---
title: 三大运营商的 OpenVPN 免流技术实现
---

## 背景介绍

国庆在家没有宽带网络，只能使用**移动卡**加上**4G路由器**的方式上网，流量唰唰唰的用，账单唰唰唰的涨。于是我开始研究是否有方法可以绕过流量计费，达到减少流量消耗的目的。经过搜索发现网上已经有很多免流上网的方案，其中"云免流"是一种通过搭建 OpenVPN 来进行云端免流的技术方式。

## 免流原理

免流的技术原理是基于运营商为了给客户提供便利而制定的特定优惠政策：

- 运营商通常会对特定业务免除流量费用，例如：
  - 接收彩信
  - 登录运营商自己的APP（如移动掌厅、电信营业厅等）
  - 访问运营商官方网站
  - 某些特定的增值业务

用户通过 OpenVPN 自身的 HTTP 代理功能，可以自定义服务器 HTTP 请求头信息，将其伪装为访问运营商网站的数据包。当运营商的计费系统检测到请求头显示用户正在访问运营商官方网站时，这部分数据流量就可能不会被计费，从而达到"免流"的效果。

## macOS 系统实现方法

虽然在手机端有很多免流应用可以直接使用，但在 macOS 系统上情况比较复杂。由于官方 OpenVPN 客户端不支持自定义 HTTP header 参数，因此我们需要自己编译经过修改的 OpenVPN 源码来实现这一功能。

### 准备工作

#### 1. 获取修改版 OpenVPN 源码

这个修改版的 OpenVPN 支持自定义 HTTP header 参数：

- 源码仓库：[OpenVpn_Patched](https://github.com/vikaskumar615/OpenVpn_Patched)
- 克隆仓库到本地：

```bash
git clone https://github.com/vikaskumar615/OpenVpn_Patched.git
cd OpenVpn_Patched
```

#### 2. 安装依赖库

编译 OpenVPN 需要用到 lzo 压缩库：

- 从官方网站下载：[LZO 官方源码](https://www.oberhumer.com/opensource/lzo/)
- 或使用 Homebrew 安装：

```bash
brew install lzo openssl
```

### 编译过程

1. 配置编译参数：

```bash
./configure --enable-password-save --disable-plugin-auth-pam \
CFLAGS="-I/usr/local/opt/openssl/include -I/usr/local/Cellar/lzo/2.09/include" \
LDFLAGS="-L/usr/local/opt/openssl/lib -L/usr/local/Cellar/lzo/2.09/lib"
```

注意：根据您安装的 lzo 和 openssl 版本，路径可能需要调整。使用 `brew info lzo` 和 `brew info openssl` 可以查看实际安装路径。

2. 编译并安装：

```bash
make
sudo make install
```

安装完成后，OpenVPN 客户端将位于 `/usr/local/sbin/openvpn`。

## 运营商免流配置

以下是几个主要运营商的免流配置示例，您需要根据实际情况调整服务器地址和证书内容。

### 中国移动免流配置

创建配置文件 `cmcc.ovpn`：

```
setenv IV_GUI_VER "de.blinkt.openvpn 0.6.17"
client
dev tun
proto tcp
connect-retry-max 5
connect-retry 5
resolv-retry 60
########免流代码########
remote wap.10086.cn 80
http-proxy-option EXT1 POST http://wap.10086.cn
http-proxy-option EXT1 Host wap.10086.cn
http-proxy-option EXT1 Host: wap.10086.cn / HTTP/1.1
http-proxy <OPENVPN服务器地址> 8080
########免流代码########
resolv-retry infinite
nobind
persist-key
persist-tun
<ca>
-----BEGIN CERTIFICATE-----
<OPENVPN证书>
-----END CERTIFICATE-----
</ca>
key-direction 1
<tls-auth>
# # 2048 bit OpenVPN static key #
-----BEGIN OpenVPN Static key V1-----
<OPENVPN证书>
-----END OpenVPN Static key V1-----
</tls-auth>
auth-user-pass pass.txt
ns-cert-type server
comp-lzo
verb 3
```

### 中国联通免流配置

创建配置文件 `unicom.ovpn`：

```
setenv IV_GUI_VER "de.blinkt.openvpn 0.6.17"
client
dev tun
proto tcp
connect-retry-max 5
connect-retry 5
resolv-retry 60
########免流代码########
remote m.client.10010.com 80
http-proxy-option EXT1 POST http://m.client.10010.com
http-proxy-option EXT1 Host m.client.10010.com
http-proxy-option EXT1 Host: m.client.10010.com / HTTP/1.1
http-proxy <OPENVPN服务器地址> 8080
########免流代码########
resolv-retry infinite
nobind
persist-key
persist-tun
<ca>
-----BEGIN CERTIFICATE-----
<OPENVPN证书>
-----END CERTIFICATE-----
</ca>
key-direction 1
<tls-auth>
# # 2048 bit OpenVPN static key #
-----BEGIN OpenVPN Static key V1-----
<OPENVPN证书>
-----END OpenVPN Static key V1-----
</tls-auth>
auth-user-pass pass.txt
ns-cert-type server
comp-lzo
verb 3
```

### 中国电信免流配置

创建配置文件 `telecom.ovpn`：

```
setenv IV_GUI_VER "de.blinkt.openvpn 0.6.17"
client
dev tun
proto tcp
connect-retry-max 5
connect-retry 5
resolv-retry 60
########免流代码########
remote wapzt.189.cn 80
http-proxy-option EXT1 POST http://wapzt.189.cn
http-proxy-option EXT1 Host wapzt.189.cn
http-proxy-option EXT1 Host: wapzt.189.cn / HTTP/1.1
http-proxy <OPENVPN服务器地址> 8080
########免流代码########
resolv-retry infinite
nobind
persist-key
persist-tun
<ca>
-----BEGIN CERTIFICATE-----
<OPENVPN证书>
-----END CERTIFICATE-----
</ca>
key-direction 1
<tls-auth>
# # 2048 bit OpenVPN static key #
-----BEGIN OpenVPN Static key V1-----
<OPENVPN证书>
-----END OpenVPN Static key V1-----
</tls-auth>
auth-user-pass pass.txt
ns-cert-type server
comp-lzo
verb 3
```

## 使用方法

1. 创建密码文件 `pass.txt`（包含VPN的用户名和密码，每个占一行）：

```
username
password
```

2. 确保配置文件和密码文件在同一目录下，然后运行：

```bash
sudo /usr/local/sbin/openvpn --config cmcc.ovpn
```

对应您使用的运营商选择相应的配置文件（cmcc.ovpn、unicom.ovpn 或 telecom.ovpn）。

## 免流效果和注意事项

1. **成功率因素**：
   - 运营商政策变化频繁，随时可能失效
   - 不同地区的效果可能不同
   - 部分基站可能有额外的流量检测机制

2. **速度限制**：
   - 免流通道通常有速度限制，不适合大流量、高速传输
   - 视频流媒体等高带宽应用可能体验不佳

3. **安全风险**：
   - 使用免费或不明来源的OpenVPN服务器存在安全风险
   - 您的网络流量可能被服务器提供者监控或记录

## 法律和道德提醒

此方法仅作为技术研究和学习交流用途分享。规避运营商计费可能：
- 违反运营商服务条款
- 存在法律风险
- 导致账号被限制或封停

**建议**：如果需要高速通畅的网络服务，请使用合法付费的网络服务。各大运营商现在都提供了多种实惠的流量套餐，选择适合自己的资费方案才是最可靠的上网方式。

## 技术发展与替代方案

随着5G网络的推广和流量资费的下降，"免流"的实用价值正在降低。目前更实用的替代方案包括：
- 选择大流量套餐
- 使用家庭宽带+随身WiFi
- 企业专线或定向流量包
