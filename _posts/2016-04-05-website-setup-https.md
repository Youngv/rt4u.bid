---
title: 从 HTTP 迁移到 HTTPS 配置 SSL 证书和 Nginx
---

由于博客中可能会存在一些敏感内容，所以担心如果采用HTTP的访问方式可能会被GFW监听到然后导致被墙的后果，
所以决定配置HTTPS的访问方式，使访问更加安全。

### 购买SSL证书
+ [PositiveSSL](https://www.ssls.com/ssl-certificates/comodo-positivessl) 购买首年只需要$3.88/年，一次性买三年$4.99/年，仅支持一个主域名
+ [PositiveSSL Wildcard](https://www.ssls.com/ssl-certificates/comodo-positivessl-wildcard) 约$90/年，支持一个主域名和无限个二级域名

### 生成 CSR Code

1. 在服务器上执行:

     >```openssl req -new -newkey rsa:2048 -nodes -keyout blueboy.key -out blueboy.csr```

2. 按提示输入以下信息：

     + Country Name 两个字母的国家代码，如CN
     + State or Province Name 省或州，如Guangdong
     + Locality Name 城市，如Guangzhou
     + Organization Name 公司名，如Tianchao
     + Organization Unit Name 集团名，如United Nations
     + Common Name 网站域名，主域名如bluboy.me，Wildcard使用*.blueboy.me
     + Email Address 邮箱地址
     + A Challenge Password 留空白即可
     + An Optional Company Name 留空白即可

3. 完成上述步骤之后会生成 blueboy.key 和 blueboy.csr 这两个文件，我们所需要的CSR CODE就在blueboy.csr文件中。

### 激活SSL证书

1. 打开[SSLS](https://www.ssls.com/user/certificates)选择证书进入激活步骤。

2. 粘贴 CSR Code。

3. 输入网站的一些信息。

4. 上传一个指定的文件到网站的根目录。

    >```scp xxxx.txt root@blueboy.me:/blog/root_dir```

5. 等待大概半个小时，SSLS会检测到根目录的文件，域名验证成功。

6. 下载生成的SSL证书上传到服务器。

    >```scp -r blueboy.me root@blueboy.me:/root```


### 配置 NGINX

1.新建 blueboy_https 文件使网站支持 HTTPS 访问

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

    add_header Strict-Transport-Security max-age=63072000;
    add_header X-Frame-Options DENY;
    add_header X-Content-Type-Options nosniff;

    location / {
      try_files $uri $uri/ =404;
    }
}
```

2.所有 HTTP 的访问自动重定向到 HTTPS

```nginx
server {
    listen 80;
    server_name blueboy.me;
    rewrite ^(.*)$  https://$host$1 permanent;
}
```

3.把网站中使用的到所有外部资源替换成可以通过 HTTPS 访问，比如七牛的图片可以开启 HTTPS 的访问方式。

至此博客就可以通过 HTTPS 访问了，HTTPS 是网站发展的趋势，也是安全发展的趋势。
