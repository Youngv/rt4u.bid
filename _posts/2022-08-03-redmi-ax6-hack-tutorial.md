---
layout: post
title: 红米 AX6 路由器破解教程
---

## 前言
本教程将指导您如何破解红米 AX6 路由器，以获得更多功能和更好的控制权。请注意，破解路由器可能会使设备保修失效，请谨慎操作。

## 红米 AX6 硬件配置
- 处理器：高通 IPQ8071A 1.4GHz 双核
- 内存：512MB RAM
- 闪存：128MB ROM
- 无线规格：
  - 2.4GHz：2x2 MIMO，最高速率 574Mbps
  - 5GHz：4x4 MIMO，最高速率 2402Mbps
- 网络接口：
  - 1 个 WAN 口（千兆）
  - 3 个 LAN 口（千兆）
- 天线：6 根外置天线
- 电源：12V/1A

## SSH 解锁方法

### 方法一：在线算号解锁
1. 获取路由器 SN 码（两种方式）：
   - 方式一：查看路由器底部贴纸上的 SN 码
   - 方式二：登录路由器管理界面（http://192.168.31.1）-> "系统设置" -> "关于"
2. 访问 [https://miwifi.dev/ssh](https://miwifi.dev/ssh)
3. 输入 SN 码计算 SSH 密码

### 方法二：离线算号解锁
1. 获取路由器 SN 码（两种方式）：
   - 方式一：查看路由器底部贴纸上的 SN 码
   - 方式二：登录路由器管理界面（http://192.168.31.1）-> "系统设置" -> "关于"
2. 使用离线算号工具 [`小米SN.html`](https://f002.backblazeb2.com/file/as-cdn/blog/%E5%B0%8F%E7%B1%B3SN.html)，可右键保存到本地
3. 打开下载的`小米SN.html`，输入 SN 码计算 SSH 密码

### 方法三：OpenWrt 解锁


参考资料
https://www.gaicas.com/xiaomi-ax3600.html
https://www.jianshu.com/p/50de90d98a4f?msclkid=bcd31ea4b43c11ec84af343cd449e85b
https://zhuanlan.zhihu.com/p/493438754
https://alphapenng.github.io/zh-cn/2022/10/06/%E7%BA%A2%E7%B1%B3-ax6-%E8%A7%A3%E9%94%81-ssh-%E5%88%B7%E6%9C%BA-openwrt-%E6%95%99%E7%A8%8B/
