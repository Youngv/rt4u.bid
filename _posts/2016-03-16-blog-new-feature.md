---
layout: single
toc: true
title: Jekyll 百度统计、Disqus 评论框和自动化部署功能
---

## 功能概述

本文介绍如何为 Jekyll 博客添加三个实用功能：百度统计分析、Disqus 评论系统以及 Git 自动化部署，这些功能将大大提升博客的用户体验和维护效率。

## 百度统计

百度统计可以帮助您了解网站的访问情况、用户行为和流量来源等数据，对优化网站内容和用户体验非常有帮助。

### 配置步骤

1. 在`_config.yml`中添加百度统计的 CODE

```yaml
baidu_analyze: 6cbd58856f0a045b48fc948c2e4679c6
```

2. 新建`_includes/baidu_analyze.html`文件

```html
{% raw %}
{% if site.baidu_analyze %}
<script>
    var _hmt = _hmt || [];
    (function() {
        var hm = document.createElement("script");
        hm.src = "//hm.baidu.com/hm.js?{{site.baidu_analyze}}";
        var s = document.getElementsByTagName("script")[0];
        s.parentNode.insertBefore(hm, s);
    })();
</script>
{% endif %}
{% endraw %}
```

3. 在`_layouts/default.html`的`<head>`标签中添加以下代码

```html
{% raw %}
{% include baidu_analyze.html %}
{% endraw %}
```

## Disqus 评论系统

Disqus 是一个流行的第三方评论系统，支持多种社交媒体登录，可以为您的静态博客添加互动功能。

### 配置步骤

1. 在[Disqus 官网](https://disqus.com/)注册账号，获取需要添加的代码

2. 新建`_includes/disqus.html`文件（注意替换以下代码中的 Disqus 网站标识符）

```html
<div id="disqus_thread"></div>
<script>
    (function() {
        var d = document, s = d.createElement('script');
        s.src = '//blueboy.disqus.com/embed.js';  // 替换为您的 Disqus 网站标识符
        s.setAttribute('data-timestamp', +new Date());
        (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>
    请启用 JavaScript 以查看<a href="https://disqus.com/?ref_noscript" rel="nofollow">由 Disqus 提供的评论。</a>
</noscript>
```

3. 在`_layouts/page.html`的文章内容后面添加

```html
{% raw %}
{% include disqus.html %}
{% endraw %}
```

## 自动化部署

通过 Git 钩子实现博客的自动化部署，可以大大简化发布流程，提高维护效率。

### 服务器配置

1. 在服务器上执行以下命令，创建 Git 仓库

```bash
mkdir ~/awesomeblog.git && cd ~/awesomeblog.git
git init --bare
vim hooks/post-receive
```

2. 在`post-receive`文件中粘贴以下代码，保存退出

```bash
#!/bin/bash -l
GIT_REPO=https://github.com/Youngv/youngv.github.io.git
TMP_GIT_CLONE=$HOME/tmp/git/awesomeblog
PUBLIC_WWW=/var/www/sites

git clone $GIT_REPO $TMP_GIT_CLONE
jekyll build --source $TMP_GIT_CLONE --destination $PUBLIC_WWW
rm -Rf $TMP_GIT_CLONE
exit
```

3. 给钩子文件添加可执行权限

```bash
chmod +x hooks/post-receive
```

### 本地配置

1. 在本地博客目录执行以下命令，添加远程部署仓库

```bash
git remote add deploy root@blueboy.me:~/awesomeblog.git
```

2. 确保服务器上的 Nginx 配置指向`/var/www/sites`目录

3. 完成更新后，执行以下命令部署博客

```bash
git push deploy master
```

## 工作原理

1. **百度统计**：当用户访问您的网站时，统计代码会收集访问数据并发送到百度统计后台。

2. **Disqus 评论**：Disqus 通过 JavaScript 加载评论系统，用户可以在您的文章下方看到评论区并参与讨论。

3. **自动化部署**：当您执行`git push deploy master`时，服务器上的 Git 钩子会自动运行，将您的博客代码拉取并构建到网站目录中。
