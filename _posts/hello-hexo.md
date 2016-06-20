---
title: Hello Hexo
date: 2016-04-21 15:30:01
tags: 
- Hexo
---
欢迎来到我的博客！ 

这是我在GitHub上的第一篇博客，使用[Hexo](https://hexo.io/)创建。

下面简单记录一下博客创建的过程，懒人就不截图了：

# 设置GitHub
访问[GitHub](https://github.com/)，注册一个新的账号。要在GitHub发送的注册确认邮件中点击确认才完成注册。

登录GitHub，选择New repository，创建一个新的代码库。

在Repository name下填写yourname.github.io，以后这就是博客的网址，点击下面的Create repository按钮。

在页面的右边点击Settings，进入设置页面，往下拉直到看到GitHub Pages，点击Automatic page generator按钮，GitHub将会自动创建一个页面。

过一会，yourname.github.io这个网址应该就可以正常访问了。

# 在Windows安装软件
需要下载安装2个软件，一个是Git，一个是Node.js。

访问[git-for-windows](https://git-for-windows.github.io/)，下载最新的Git，我下载的是2.8.1，安装一路Next。
安装好后，在命令行执行以下命令查看git版本号，能执行就说明安装好了。
``` shell
>git --version
```

访问[Nodejs](https://nodejs.org/en/)，下载最新版的Nodejs，我下载是4.4.3，安装也是一路Next。
安装好后，在命令行执行以下命令查看node和npm版本号，能执行就说明安装好了。
``` shell
>node -v
>npm -v
```

# 安装Hexo
在资源管理器找个位置创建并打开一个文件夹，在文件夹空白处按住shift键+鼠标右键，选“在此处打开命令窗口”，以后的命令均在这个目录下执行。

顺序执行以下命令：
``` shell
>npm install hexo-cli -g
>npm install hexo --save
```

安装好后，执行以下命令查看hexo版本号，能执行就说明安装好了。
``` shell
>hexo -v
```

# 初始化Hexo
顺序执行以下命令：
``` shell
>hexo init
>npm install
```
这时在目录下会创出几个目录和文件，初始化就好了。

# 体验Hexo
Hexo主要的配置文件是_config.yml，用文本编辑器打开，按自己喜好修改以下一些内容，如：
``` text
title: 博客标题
subtitle: 子标题
description: 博客描述
author: 作者
language:
timezone:

url: http://yoursite.com
```
如果要设置为简体中文，language需要写成 `zh-Hans`。
时区不用设置，会自动按照当前电脑的时区。

在source/_posts目录下，有一个hello-world.md文件，可以用文本编辑器打开写点自己的内容。

md文件是Markdown格式，见[Markdown 语法 (简体中文版)](https://github.com/riku/Markdown-Syntax-CN)

在命令行执行以下命令：
``` shell
>hexo g
>hexo s
```
第一条命令是根据当前内容，生成博客的静态页面，生成的内容在public目录下。
第二条命令是建立一个web server，可以用浏览器访问 127.0.0.1:4000 看看目前博客的样子。

这两条命令可以简化为一条命令：`hexo s -g`

# 部署到GitHub
执行`hexo g`后，网站的静态页面就生成在了public目录下，将其中的内容Push到GitHub就好了，可以自己用Git来Push，也可以设置Hexo来Push。

要用Hexo来Push到GitHub，先执行以下命令，安装一个插件：
``` shell
>npm install hexo-deployer-git --save
```

再执行如下两条命令设置GitHub的用户名和邮箱，就是第一步在GitHub注册的。
``` shell
>git config --global user.name "yourname"
>git config --global user.email "youremail"
```

在Git Bash中创建SSH key
``` bash
$ ssh-keygen -t rsa -C "yourname@youremail.com"
```
创建的key在用户目录下的.ssh目录下，将id_rsa.pub文件的内容加入GitHub用户设置的SSH Key里。

用文本编辑器打开_config.yml文件，编辑deploy信息：
``` text
deploy:
  type: git
  repo: git@github.com:yourname/yourname.github.io.git
  branch: master
```

执行以下命令将生成都网页push到GitHub上
``` shell
>hexo d
```

push成功后，访问yourname.github.io就能看到了。

# 写新文章
``` shell
>hexo n "article-name"
```
其中的article-name根据实际设定。

然后在 source\_posts 目录下，找到article-name.md文件，可以用文本编辑器打开编辑。

编辑完成后，执行如下命令重新生成博客内容并部署到GitHub上：
``` shell
>hexo d -g
```


# 主要参考文章：
[史上最详细的Hexo博客搭建图文教程](https://xuanwo.org/2015/03/26/hexo-intor/)
[如何搭建一个独立博客——简明Github Pages与Hexo教程](http://www.jianshu.com/p/05289a4bc8b2)

