---
title: 创建Tags页面
date: 2016-04-22 10:54:23
tags: 
- Hexo
---
默认用Hexo创建的博客有个Tags链接，但点进去却显示404，我讨厌这个页面。

在网上搜了下，需要自己创建一个tags页面，在Hexo目录下，运行：
``` shell
>hexo new page tags
```
会在sources目录下创建一个tags目录，里面有个index.md文件，用文本编辑器打开文件，里面的内容：
``` text
--- 
title: tags
date: 2016-04-22 10:42:13
---
```
在date后面增加一行：
``` text
type: "tags"
```
保存

然后顺序执行
``` shell
>hexo clean
>hexo g
>hexo s
```

用浏览器访问：127.0.0.1:4000，点Tags，就不会显示404了。