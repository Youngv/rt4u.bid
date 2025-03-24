---
layout: post
title: 移动光猫 GM219-S 改桥接模式详细教程
---

## 起因

家里网络不稳定，WiFi 经常卡顿，断电重启后恢复慢。检查发现 GM219-S 光猫发烫，因配置太低且承担太多功能：光信号转换、PPPoE 拨号、路由和 WiFi，决定改为桥接模式，让专业路由器接管网络功能。

## 桥接优势

- 减轻光猫负担，只负责光电信号转换
- 避免双重 NAT 问题，开启 NAT1
- 桥接软路由后，可以实现更多网络功能

## 登录光猫后台

### 获取超级管理员账号

| 方法 | 详情 |
|------|------|
| **方法 1：常用密码组合** | `CMCCAdmin` / `aDm8H%MdA` |
| **方法 2：社工获取** | 找宽带小哥获取超级管理员密码 |
| **方法 3：技术破解** | 见下方步骤 |

### 技术破解步骤

#### 1. 开启 Telnet 访问

在浏览器地址栏输入以下地址启用 telnet：
```
http://192.168.1.1/usr=CMCCAdmin&psw=aDm8H%25MdA&cmd=1&telnet.gch
```

#### 2. 通过 Telnet 连接光猫
- Win + R 输入 `control` 命令到控制面板 --> 程序 --> 程序和功能 --> 启动和关闭 Windows 功能 --> Telnet 客户端 --> 勾选 --> 确定

- 在 Windows 中打开命令提示符，输入：
  ```
  telnet 192.168.1.1
  ```

- 使用 root 账号登录：
  ```
  用户名：root
  密码：Pon521
  ```

- 将配置文件复制到公共目录：
  ```
  cp /userconfig/cfg/db_user_cfg.xml /home/httpd/public/img/db_user_cfg.xml
  ```

#### 3. 下载配置文件

打开浏览器访问 [http://192.168.1.1/img/db_user_cfg.xml](http://192.168.1.1/img/db_user_cfg.xml)，保存到本地 `db_user_cfg.xml`

#### 4. 查看账号密码

1. 下载 [Router Password Recovery](https://www.nirsoft.net/utils/router_password_recovery.html) 工具

2. 使用该工具打开下载的 `db_user_cfg.xml` 文件

3. 搜索 `CMCCAdmin` 找到管理密码：
   ```xml
    <DM name="User" val="CMCCAdmin"/>
    <DM name="Pass" val="n1yT%$mb"/>
   ```

4. 找到宽带账号密码相关信息：
   ```xml
   <DM name="UserName" val="17899999999@139.gd"/>
   <DM name="Password" val="123456"/>
   ```

> **重要提示**：配置桥接时需要使用这些信息。

## 配置桥接模式

### 第一步：登录光猫超级管理员
- 浏览器访问 [http://192.168.1.1](http://192.168.1.1)
- 使用上面获取的超级管理员账号 `CMCCAdmin`/`n1yT%$mb` 登录

### 第二步：记录当前网络设置设置
- 网络 - Internet 连接 - 宽带设置
- 连接名称（含 INTERNET_R）
- VLAN ID（如 46、41、43 等）
- 绑定端口如 LAN1

### 第三步：删除当前拨号连接

1. 网络 - Internet 连接 - 宽带设置
2. 找到 INTERNET_R 路由连接
3. 删除

### 第四步：创建桥接连接

1. 连接名称下拉选择新建
2. 设置参数：

   | 参数 | 设置值 |
   |------|--------|
   | 模式 | `Bridge` |
   | 服务类型 | `Internet` |
   | IP 协议版本 | `IPv4/v6` |
   | 绑定端口 | 选择连接路由器的 LAN 口 |
   | VLAN ID | **与原连接相同** |
   | 启用 | 勾选 |

3. 保存设置

> **注意**：如绑定选项灰色无法选择，需先取消原连接的绑定选项。

### 第五步：测试桥接

1. 路由器 WAN 口连接光猫 LAN 口
2. 配置路由器 PPPoE 拨号，使用上面的宽带账号密码连接
3. 检测是否能自动获取到 IP 地址

## 参考资料

1. [光猫 GM219-s 改桥接以及常见问题](https://blog.csdn.net/u010263423/article/details/133375594)
2. [笔记：中国移动光猫 GM219-S 获取超管密码](https://www.bilibili.com/opus/575523027808714585)
3. [移动光猫 GM219-S 超级密码获取教程](https://blog.csdn.net/MUZIYUQI/article/details/134637437)
4. [四川移动版吉比特 GM219-S\H2-2 光猫改桥接模式](https://blog.ppgg.in/10042.html)
