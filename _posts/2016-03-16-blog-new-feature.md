---
title: Jekyll 百度统计、Disqus 评论框和自动化部署功能
---

### 百度统计

1.在`_config.yml`中添加百度统计的CODE

```shell
baidu_analyze: 6cbd58856f0a045b48fc948c2e4679c6
```

2.新建`_includes/baidu_analyze.html`文件

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

3.在`_layouts/default.html`的head标签中添加代码

```html
{% raw %}
{% include baidu_analyze.html %}
{% endraw %}
```


### Disqus评论框

1.在[Disqus](https://disqus.com/)官网注册帐号，获取到需要添加的代码。

2.新建`_includes/disqus.html`文件（代码中的disqus地址不同）

```html
<div id="disqus_thread"></div>
<script>
    (function() {
        var d = document, s = d.createElement('script');
        s.src = '//blueboy.disqus.com/embed.js';
        s.setAttribute('data-timestamp', +new Date());
        (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by Disqus.</a></noscript>
```

3.在`_layouts/page.html`的文章内容后面添加

```html
{% raw %}
{% include disqus.html %}
{% endraw %}
```

### 自动化部署

1.在自己的服务器上运行下列命令

```shell
mkdir ~/awesomeblog.git && cd ~/awesomeblog.git
git init --bare
vim hooks/post-receive
```

2.粘贴以下代码保存退出

```shell
#!/bin/bash -l
GIT_REPO=https://github.com/Youngv/youngv.github.io.git
TMP_GIT_CLONE=$HOME/tmp/git/awesomeblog
PUBLIC_WWW=/var/www/sites

git clone $GIT_REPO $TMP_GIT_CLONE
jekyll build --source $TMP_GIT_CLONE --destination $PUBLIC_WWW
rm -Rf $TMP_GIT_CLONE
exit
```

3.给文件可执行权限

```shell
chmod +x hooks/post-receive
```

4.本地博客目录执行下面的命令

```shell
git remote add deploy root@blueboy.me:~/awesomeblog.git
```

5.服务器上的 nginx 指向上面`/var/www/sites`的目录

6.本地博客目录执行

```shell
git push deploy master
```
