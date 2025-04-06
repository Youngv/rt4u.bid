---
layout: single
toc: true
title: 红米 AX6 路由器解锁 SSH 教程
---

## 前言
本教程将指导您如何破解红米 AX6 路由器，以获得更多功能和更好的控制权。

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

### 方法三：使用 OpenWrt 路由器无线中继解锁
1. **准备工作**
   - 一台电脑
   - 一台已经刷了 OpenWrt 的路由器
   - 下载红米 AX6 1.0.18 降级固件：
     [miwifi_ra69_firmware_45a77_1.0.18.bin](https://f002.backblazeb2.com/file/as-cdn/blog/miwifi_ra69_firmware_45a77_1.0.18.bin)
   - 下载 GitHub 解锁脚本：
     [unlock-redmi-ax3000](https://github.com/shell-script/unlock-redmi-ax3000/archive/refs/heads/master.zip)

2. **降级并恢复出厂设置**
   - 打开红米 AX6 路由器后台
   - 选择"系统升级"—"手动升级"
   - 选择下载好的 1.0.18 固件
   - 勾选"恢复出厂设置"
   - 点击升级并等待重启完成

   > **⚠️ 警告**：固件降级有风险，请确保操作过程中路由器电源稳定，避免中途断电导致变砖！

3. **准备 OpenWrt 服务**
   - 将已刷 OpenWrt 的路由器接上电源，确保有 Wi-Fi 信号
   - 使用网线将电脑连接到 OpenWrt 路由器
   - 将解压出的 wireless.sh 脚本上传到 OpenWrt 路由器：
     ```bash
     scp wireless.sh root@10.0.0.1:/root/wireless.sh
     ```
     注意：IP 地址根据实际 OpenWrt 路由器地址修改

   - SSH 连接到 OpenWrt 路由器：
     ```bash
     ssh root@10.0.0.1
     ```

   - 执行无线中继脚本（注意：执行此脚本会更改您的网络和无线设定，执行前请务必备份相关数据）：
     ```bash
     sh /root/wireless.sh
     ```

4. **红米 AX6 破解 SSH**
   - 获取后台 STOK：
     - 登录小米路由器后台
     - 在浏览器地址栏找到 stok= 后面的一段内容（选中部分）并复制备用

   - 发送第一次请求：
     - 在浏览器中访问以下地址（替换 `<STOK>` 为刚才复制的内容）：
     ```
     http://192.168.31.1/cgi-bin/luci/;stok=<STOK>/api/misystem/extendwifi_connect?ssid=MEDIATEK-ARM-IS-GREAT&password=ARE-YOU-OK
     ```
     - 请求成功会显示 `{"msg":"connect succces!","code":0}`

   - 发送第二次请求：
     - 在浏览器中访问以下地址（替换 `<STOK>` 为之前复制的内容）：
     ```
     http://192.168.31.1/cgi-bin/luci/;stok=<STOK>/api/xqsystem/oneclick_get_remote_token?username=xxx&password=xxx&nonce=xxx
     ```
     - 请求成功会返回含有 token 的 JSON 数据

5. **验证 SSH 连接**
   - 通过 SSH 连接到小米路由器：
     ```bash
     ssh root@192.168.31.1
     ```
   - 用户名为：root
   - SSH 密码为：小米路由器后台 Wi-Fi 的密码

   > **💡 提示**：如果连接失败，可能需要等待几分钟后再次尝试，或重启路由器后再试。

## 刷入 OpenWrt 固件

### 1. 下载固件
- 需要下载的文件：
  - [xiaomimtd12.bin](https://f002.backblazeb2.com/file/as-cdn/blog/xiaomimtd12.bin) (QSDK 固件)
  - [a6minbib.bin](https://f002.backblazeb2.com/file/as-cdn/blog/a6minbib.bin) (分区表文件)
  - [openwrt-ipq807x-generic-xiaomi_ax6-squashfs-nand-factory.bin](https://github.com/Youngv/OpenWrt-AX6/releases) (OpenWrt 固件)

### 2. 设置环境变量
- SSH 连接到红米 AX6 路由器：
  ```bash
  ssh root@192.168.31.1
  ```

- 执行以下命令设置环境变量：
  ```bash
  nvram set flag_last_success=0
  nvram set flag_boot_rootfs=0
  nvram set flag_boot_success=1
  nvram set flag_try_sys1_failed=0
  nvram set flag_try_sys2_failed=0
  nvram set boot_wait=on
  nvram set uart_en=1
  nvram set telnet_en=1
  nvram set ssh_en=1
  nvram commit
  ```

### 3. 刷入固件
- 将下载好的 xiaomimtd12.bin 上传到红米 AX6 的 /tmp 目录：
  ```bash
  scp xiaomimtd12.bin root@192.168.31.1:/tmp
  ```

- 在红米 AX6 上执行刷写命令：
  ```bash
  mtd write /tmp/xiaomimtd12.bin rootfs
  ```

- 断电重启红米 AX6，此时路由器的后台地址已变为 `192.168.1.1`

  > **⚠️ 警告**：以下步骤非常关键，错误操作可能导致路由器变砖！请确保每一步都正确执行，并保持电源稳定。

### 4. 重新分区
- 将下载好的 a6minbib.bin 上传到红米 AX6 的 /tmp 目录：
  ```bash
  scp a6minbib.bin root@192.168.1.1:/tmp
  ```

- 在红米 AX6 上执行以下命令：
  ```bash
  . /lib/upgrade/platform.sh
  switch_layout boot; do_flash_failsafe_partition a6minbib "0:MIBIB"
  ```

- 拔电源重启路由器

### 5. 刷入 OpenWrt 到 rootfs_1 分区
- 将下载好的 OpenWrt 固件上传到红米 AX6 的 /tmp 目录：
  ```bash
  scp openwrt-ipq807x-generic-xiaomi_ax6-squashfs-nand-factory.bin root@192.168.1.1:/tmp
  ```

- 在红米 AX6 上执行刷写命令：
  ```bash
  ubiformat /dev/mtd13 -y -f /tmp/openwrt-ipq807x-generic-xiaomi_ax6-squashfs-nand-factory.bin
  fw_setenv flag_last_success 1
  fw_setenv flag_boot_rootfs 1
  ```

- 重启路由器使配置生效：
  ```bash
  reboot
  ```

- 重启完成后，系统已成功刷入 OpenWrt

## 刷回官方系统

### 1. 下载官方固件
- 下载两个必要的固件文件：
  - [AX6_1.1.4.ubi](https://f002.backblazeb2.com/file/as-cdn/blog/AX6_1.1.4.ubi)：官方系统固件
  - [ybmibib.bin](https://f002.backblazeb2.com/file/as-cdn/blog/ybmibib.bin)：分区表文件

### 2. 上传固件到路由器
- 使用 SSH 连接到路由器：
  ```bash
  ssh root@192.168.1.1
  ```
- 将下载好的 `AX6_1.1.4.ubi` 和 `ybmibib.bin` 两个文件上传到路由器的 `/tmp` 目录

### 3. 执行刷机命令
- 依次执行以下命令：
  ```bash
  # 刷入官方系统固件
  ubiformat /dev/mtd12 -y -f /tmp/AX6_1.1.4.ubi
  
  # 刷入分区表
  mtd write /tmp/ybmibib.bin /dev/mtd1
  
  # 设置环境变量
  fw_setenv flag_last_success 0
  fw_setenv flag_boot_rootfs 0
  ```

  > **⚠️ 警告**：刷回官方固件后，所有自定义设置将会丢失，请确保已备份重要配置。

### 4. 重启路由器
- 刷机命令执行完成后，拔掉路由器电源插头然后重新接上
- 重启过程可能会较长，请耐心等待
- 请确保重启过程中不要断电，以免造成固件损坏
- 当电脑能够获取到路由器分配的 IP 地址并可以进入路由器控制台时，表示刷回操作已完成
- 此时路由器应已恢复为官方系统，默认管理地址为 `192.168.31.1`

## 本地安装 ShellClash

### 1. 下载 ShellClash 软件包
- 访问 [ShellCrash GitHub 发布页](https://github.com/juewuy/ShellCrash/releases)
- 下载最新的 `ShellCrash.tar.gz` 文件
- 也可参考 [官方文档](https://juewuy.github.io/bdaz/) 了解更多详情

### 2. 搭建本地文件服务器
- 在电脑上安装 [HFS](https://rejetto.com/hfs/) 或其他文件服务器软件
- 将下载的 `ShellCrash.tar.gz` 放入共享目录
- 启动 HFS 服务，记录服务器地址 (例如 `http://192.168.10.2/Sync/`)

### 3. 在路由器上安装 ShellClash
- 通过 SSH 连接到路由器：
  ```bash
  ssh root@192.168.1.1
  ```

- 下载 ShellCrash 软件包到路由器 /tmp 目录：
  ```bash
  wget -q -O /tmp/ShellCrash.tar.gz http://192.168.10.2/Sync/ShellClash.tar.gz
  ```
  注意：请将 URL 替换为您实际的本地服务器地址

- 验证文件下载是否成功：
  ```bash
  ls -la /tmp/ShellCrash.tar.gz
  ```

- 解压并安装 ShellClash：
  ```bash
  mkdir -p /tmp/SC_tmp && tar -zxf '/tmp/ShellCrash.tar.gz' -C /tmp/SC_tmp/ && source /tmp/SC_tmp/init.sh
  ```

- 根据安装向导完成配置，选择合适的安装位置和相关选项

## SSH 连接配置
```bash
Host AX6
    ForwardAgent yes
    HostName 192.168.10.1
    User root
    ServerAliveInterval 60
    IdentityFile ~/.ssh/id_rsa
    IdentitiesOnly yes
    HostkeyAlgorithms +ssh-rsa
    PubkeyAcceptedAlgorithms +ssh-rsa
```

## 升级后重新开启 SSH
```bash
telnet 192.168.10.1
sed -i 's/channel=.*/channel="debug"/g' /etc/init.d/dropbear
/etc/init.d/dropbear start
```

## 参考资料
1. [小米路由器 AX3600 解锁 SSH 启用科学上网](https://www.gaicas.com/xiaomi-ax3600.html)
2. [红米 AX6 解锁 SSH](https://www.jianshu.com/p/50de90d98a4f?msclkid=bcd31ea4b43c11ec84af343cd449e85b)
3. [RedMi AX6 刷机 OpenWrt](https://zhuanlan.zhihu.com/p/493438754)
4. [红米 AX6 解锁 SSH 安装 ShellClash 及刷机 Openwrt 教程](https://alphapenng.github.io/zh-cn/2022/10/06/%E7%BA%A2%E7%B1%B3-ax6-%E8%A7%A3%E9%94%81-ssh-%E5%88%B7%E6%9C%BA-openwrt-%E6%95%99%E7%A8%8B/)
5. [红米 AX6 SSH 解锁和挂载 overlay 方法](https://www.right.com.cn/forum/thread-4060726-1-1.html)
