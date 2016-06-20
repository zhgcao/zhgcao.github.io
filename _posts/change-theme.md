---
title: 把博客的主题切换为NexT
date: 2016-04-21 16:31:01
tags: [Hexo, NexT]
---
Hexo默认安装的主题是landscape，从网上看到NexT的主题挺简洁漂亮的，就改用它了。

## 下载NexT
先把Next主题下载下来，可以用两种方式：
1、用Git克隆下来
``` shell
>cd your-hexo-site
>git clone https://github.com/iissnan/hexo-theme-next themes/next
```

2、直接下载稳定版本，前往 NexT 版本 [发布页面](https://github.com/iissnan/hexo-theme-next/releases)。
选择你所需要的版本，下载 Download 下的 Source Code (zip) 到本地。目前最新的是5.0.1版本。

将下载下来的文件解压到 themes 目录，并将创建的文件夹更名为next。

## 设置Hexo使用NexT主题
用文本编辑器打开Hexo目录下的_config.yml文件，修改theme
``` text
theme: next
```

在命令行顺序执行
``` shell
>hexo clean
>hexo g
>hexo s
```

用浏览器访问：127.0.0.1:4000，就能看到新的主题样子了。

更多信息请看[开始使用NexT](http://theme-next.iissnan.com/getting-started.html)。
