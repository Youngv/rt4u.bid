---
title: 三大运营商的 OpenVPN 免流
---

国庆在家没有宽带网络，只能使用**移动卡**加上**4G路由器**的方式上网，流量唰唰唰的用，账单唰唰唰的涨，
于是我想有没有方法可以绕过的流量计费，达到免流量上网的目的，经过搜索发现网上已经有很多免流上网的方案，
其中的云免流是一种搭建 OpenVPN 来进行云端免流的方式。

免流的原理是运营商为了给客户提供方便，提供了优惠政策，如：接收彩信、登陆掌厅免除流量费以及免收取流量费的其他业务。
用户通过 OpenVPN 自身的 HTTP 代理自定义服务器 HTTP 请求头信息为运营商网站，当系统检测到请求头属于访问运营商网站的数据，
用户即可达到免费上网的目的。

客户端免流有很多应用提供利用手机接口创建 OpenVPN，但是在 macOS 上由于官方 OpenVPN 不支持自定义 header 参数，
只能自己编译别人修改过的支持自定义 header 参数的 OpenVPN 源码。

#### OpenVpn_Patched

[源码地址](https://github.com/vikaskumar615/OpenVpn_Patched)

编译 OpenVPN 需要用到 lzo 包

#### lzo

[源码地址](https://www.oberhumer.com/opensource/lzo/) 或者 `brew install lzo`

#### 编译

```
./configure --enable-password-save --disable-plugin-auth-pam CFLAGS="-I/usr/local/opt/openssl/include -I/usr/local/Cellar/lzo/2.09/include" LDFLAGS="-L/usr/local/opt/openssl/lib -L/usr/local/Cellar/lzo/2.09/lib"
```

#### 移动.ovpn 配置文件

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

#### 教训

此方法仅作为学习交流用途，如果需要高速通畅的网络，还是使用付费服务吧。
