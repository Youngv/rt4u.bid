---
layout: post
title: 从 HTTP 迁移到 HTTPS 配置 SSL 证书和 Nginx
---

## 概述

由于博客中可能会存在一些敏感内容，所以担心如果采用HTTP的访问方式可能会被GFW监听到然后导致被墙的后果，所以决定配置HTTPS的访问方式，使访问更加安全。HTTPS通过SSL/TLS加密传输数据，不仅可以增强网站安全性，还能提高SEO排名和用户信任度。

## SSL证书选择与购买

选择合适的SSL证书是配置HTTPS的第一步。根据不同需求，可以选择以下类型的证书：

- [PositiveSSL](https://www.ssls.com/ssl-certificates/comodo-positivessl) 
    - 价格实惠，首年只需要$3.88/年
    - 一次性买三年$4.99/年
    - 仅支持一个主域名
    - 适合个人博客或小型网站
- [PositiveSSL Wildcard](https://www.ssls.com/ssl-certificates/comodo-positivessl-wildcard) 
    - 价格约$90/年
    - 支持一个主域名和无限个二级域名
    - 适合有多个子域名的企业网站

## 生成 CSR 代码

CSR（Certificate Signing Request）是向证书颁发机构申请SSL证书时必需的代码。以下是生成CSR的步骤：

1. 在服务器上执行以下命令，生成私钥和CSR文件：

   ```bash
   openssl req -new -newkey rsa:2048 -nodes -keyout blueboy.key -out blueboy.csr
   ```

2. 根据提示输入以下信息：

   | 字段 | 说明 | 示例 |
   | --- | --- | --- |
   | Country Name | 两个字母的国家代码 | CN |
   | State or Province Name | 省或州名称 | Guangdong |
   | Locality Name | 城市名称 | Guangzhou |
   | Organization Name | 公司或组织名称 | Tianchao |
   | Organization Unit Name | 部门名称 | United Nations |
   | Common Name | 网站域名（主域名或泛域名） | blueboy.me 或 *.blueboy.me |
   | Email Address | 联系邮箱 | your@email.com |
   | A Challenge Password | 可留空 | - |
   | An Optional Company Name | 可留空 | - |

3. 完成上述步骤后会生成两个文件：
   - `blueboy.key`：私钥文件，**必须妥善保管，不要泄露**
   - `blueboy.csr`：CSR文件，包含向证书颁发机构申请证书的信息

## 激活 SSL 证书

1. 登录 [SSLS.com](https://www.ssls.com/user/certificates) 账户，选择已购买的证书进入激活流程

2. 将 `blueboy.csr` 文件中的内容粘贴到指定位置（CSR Code输入框）

3. 输入网站的基本信息，如管理员联系方式等

4. 选择域名验证方式，这里使用文件验证法：
   - 下载指定验证文件
   - 上传到网站根目录，确保可以通过 `http://blueboy.me/xxxx.txt` 访问

   ```bash
   # 将验证文件上传到服务器网站根目录
   scp xxxx.txt root@blueboy.me:/blog/root_dir
   ```

5. 等待域名验证（通常需要30分钟左右），证书颁发机构会检测根目录的验证文件

6. 证书生成后，下载并上传到服务器：

   ```bash
   # 将证书文件上传到服务器
   scp -r blueboy.me root@blueboy.me:/root
   ```

## 配置 Nginx 支持 HTTPS

### 创建 HTTPS 服务器配置

1. 新建 Nginx 配置文件，例如 `/etc/nginx/sites-available/blueboy_https`：

   ```nginx
   server {
       listen 443 ssl;
       listen [::]:443 ssl ipv6only=on;
       root /home/blog/data;
       index index.html index.htm;
       server_name blueboy.me;

       ssl on;
       ssl_certificate /root/blueboy.me/blueboy.me.crt;
       ssl_certificate_key /root/blueboy.me/blueboy.key;

       # 增强安全性配置
       ssl_protocols TLSv1.2 TLSv1.3;  # 推荐使用的TLS版本
       ssl_prefer_server_ciphers on;
       ssl_ciphers 'ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384';
       ssl_session_timeout 1d;
       ssl_session_cache shared:SSL:10m;

       # HSTS (HTTP Strict Transport Security)
       add_header Strict-Transport-Security "max-age=63072000; includeSubdomains";
       add_header X-Frame-Options DENY;
       add_header X-Content-Type-Options nosniff;
       add_header X-XSS-Protection "1; mode=block";

       location / {
           try_files $uri $uri/ =404;
       }
   }
   ```

### 配置 HTTP 重定向

2. 修改原有的 HTTP 配置，将所有 HTTP 请求重定向到 HTTPS：

   ```nginx
   server {
       listen 80;
       listen [::]:80;
       server_name blueboy.me;
       
       # 永久重定向到HTTPS
       return 301 https://$host$request_uri;
   }
   ```

### 启用配置并重启 Nginx

3. 创建符号链接并测试配置：

   ```bash
   sudo ln -s /etc/nginx/sites-available/blueboy_https /etc/nginx/sites-enabled/
   sudo nginx -t
   ```

4. 如果测试通过，重启 Nginx 服务：

   ```bash
   sudo systemctl restart nginx
   ```

## 网站资源适配

为了避免混合内容警告（Mixed Content），需要确保网站中的所有资源都通过HTTPS加载：

1. 将所有外部资源的URL从`http://`更改为`https://`，包括：
   - JavaScript 文件
   - CSS 样式表
   - 图片和媒体文件
   - 外部API和服务
2. 对于第三方服务和CDN：
   - 七牛云等CDN服务需要在管理面板中开启HTTPS访问
   - 使用相对路径引用资源（如 `//example.com/script.js`）可以自动匹配协议

## 验证 HTTPS 配置

完成上述步骤后，可以通过以下方式验证HTTPS配置：

1. 通过浏览器访问 `https://blueboy.me`，确认能正常加载并显示安全锁图标
2. 使用 [SSL Labs](https://www.ssllabs.com/ssltest/) 测试网站SSL配置安全性
3. 检查控制台是否有混合内容警告

## 结语

至此，博客已成功从HTTP迁移到HTTPS。HTTPS不仅是网站发展的趋势，也是保护用户隐私和数据安全的重要措施。随着浏览器对非HTTPS网站的警告越来越严格，及时迁移到HTTPS对网站的可访问性和SEO排名都有积极影响。
