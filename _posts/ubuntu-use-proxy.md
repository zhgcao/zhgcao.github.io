---
title: Ubuntu设置使用代理
date: 2016-06-02 14:33:03
tags: [Ubuntu, Git, Docker]
---
为了方便测试一些命令和工具，在公司用VirtualBox安装了一个Ubuntu的Server版，Server版默认没有图像界面，跑起来对系统资源的占用比较少，很适合用于虚拟机，特别是在公司用了很多年的低配置电脑上。

在公司上网需要通过代理，这里简要介绍一下Ubuntu几个可能需要设置代理的地方。

## 设置apt.conf
Ubuntu在安装时就会提示输入代理的设置，设置的代理保存在 /etc/apt/apt.conf 文件里，文件内容格式如下：
``` text
Acquire::http::Proxy "http://<proxy.server>:<port>/";
```

如果安装时没设置代理，或代理有变化，以后也可以编辑修改这个文件。

这样设置后，apt-get就可以正常使用了。

## 设置环境变量
只设置apt.conf，wget或git等软件还是不能访问网络，这时可以再设置2个环境变量：
``` bash
$ export http_proxy=http://<proxy.server>:<port>/
$ export https_proxy=http://<proxy.server>:<port>/
```

或者将这2条命令添加到 ~/.bashrc 结尾。
如果访问的代理需要用户名和密码，上面的代理设置可以写成 `http://<user>:<password>@<proxy.server>:<port>/` 。
有了这两个环境变量，wget或git（通过http/https）等命令就可以正常访问网络了。

## wget的设置
对于wget，还可以编辑/etc/wgetrc，找到如下内容，去掉设置项前的`#`号并修改其中的内容：
``` text
# You can set the default proxies for Wget to use for http, https, and ftp.
# They will override the value in the environment.
#https_proxy = http://proxy.yoyodyne.com:18023/
#http_proxy = http://proxy.yoyodyne.com:18023/
#ftp_proxy = http://proxy.yoyodyne.com:18023/

# If you do not want to use proxy at all, set this to off.
#use_proxy = on
```

或者在主目录下新建一个文件 ~/.wgetrc，将上面那段内容复制过来并按实际修改。

如果想临时使用一个代理，不想修改配置文件或设置环境变量，可以在wget命令中增加 `-e "xxx"` 选项，这个选项是执行一个wgetrc格式的命令。
命令格式如下：
``` bash
$ wget -e "use_proxy=on" -e "http_proxy=http://<proxy.server>:<port>/" <url>
```

## Git的设置
Git支持Git、SSH、HTTP/HTTPS三种协议访问远程仓库，3钟协议的远程仓库地址格式不同，以 GitHub 为例：
Git协议：`git://github.com/...`
SSH协议：`git@github.com:...`
HTTP/HTTPS协议：`http://github.com/...` 或 `https://github.com/...`

上面设置的环境变量，可以让Git使用HTTP/HTTPS协议通过代理访问远程仓库。

除了设环境变量，Git也可以通过如下命令设置http的代理：
``` bash
$ git config --global http.proxy http://<user>:<password>@<proxy.server>:<port>
```

如果要通过Git协议访问远程仓库，可以先安装一个软件 connect-proxy （其他发行版可能叫 proxy-connect）：
``` bash
$ sudo apt-get install connect-proxy
```

然后在 /usr/bin 下新建一个文件 httpproxywrapper ，内容如下：
``` text
#!/bin/sh
connect -H <proxy.server>:<port> "$@"
```
上面的 -H 表示是Http代理，如果是socks代理，需要改为 -S。

给 httpproxywrapper 设置可执行权限
``` bash
$ sudo chmod +x /usr/bin/httpproxywrapper
```

然后设置 git 使用代理：
``` bash
$ git config --global core.gitproxy /usr/bin/httpproxywrapper
```
或设置环境变量：
``` bash
$ export GIT_PROXY_COMMAND="/usr/bin/httpproxywrapper"
```

这样设置，就可以用Git协议通过代理访问远程仓库了。

至于Git的SSH协议，网上找了几种方法，但经测试均无法通过http代理访问远程仓库，猜测可能和公司的代理设置有关，就不折腾了。

## Docker的设置 
Docker要使用代理，需要修改 `etc/default/docker` 这个文件，在其中增加如下几行：
``` text
http_proxy=http://<proxy.server>:<port>
https_proxy=http://<proxy.server>:<port>
export http_proxy https_proxy
```
需要注意，原文档中有一行注释 `#export http_proxy="http://127.0.0.1:3128/"`，但按照这样的格式直接写 `export http_proxy=http://<proxy.server>:<port>` 却不生效。

修改后，需要重启Docker才能使设置生效：
``` bash
$ sudo systemctl restart docker
```

用以下命令查看设置是否成功：
``` bash
$ sudo docker info
```
在返回信息中看到有以下两行就表明OK了：
``` text
Http Proxy: http://<proxy.server>:<port>
Https Proxy: http://<proxy.server>:<port>
```
