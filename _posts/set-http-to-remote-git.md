---
title: 通过http(s)协议访问自建的Git远程仓库
date: 2016-06-25 12:35:51
tags: [Git, Nginx]
---
上一篇[Git自建远程仓库](/2016/06/14/git-create-remote-server/)，里面创建的远程库，是通过SSH协议来连接的，除了SSH协议，Git还支持通过http(s)协议或Git协议来访问远程库，配置也不太复杂。

## Http协议
以Ubuntu为例，可以通过Nginx来配置http(s)访问Git远程库，系统需要先安装了Nginx，如果没有安装，可以用以下命令安装：
``` bash
# apt-get install nginx
```

假设仓库放在/home/git目录下，在此目录下创建一个新的裸仓库，库名称为sample.git：
``` bash
# git init --bare sample.git
```

修改新建的库的权限，让所有人均可修改，因为通过http访问时，默认用户是`www-data`，不改权限会不能push。
``` bash
# chmod -R a+w sample.git
```

可以让Nginx来管理访问时的用户鉴权，用以下命令增加一个git用户并按提示设置密码，可以创建多个用户。
``` bash
# htpasswd [-c] /etc/nginx/passwd git
```
如果提示没有`htpasswd`命令，需要先安装apache2-utils包：`apt-get install apache2-utils`。
`/etc/nginx/passwd`为用户名和密码文件，`-c` 参数表示创建一个新的密码文件，原来没有这个文件时必须要带，已经存在这个文件了就不要带`-c`参数了。

接着安装`fcgiwrap`包
``` bash
# apt-get install fcgiwrap
```

修改Nginx的配置文件，增加如下内容：
``` text
        # 配置以 /git 开始的虚拟目录
        location ~ /git(/.*) {
                # 使用 Basic 认证
                auth_basic "Restricted";
                # 认证的用户文件
                auth_basic_user_file /etc/nginx/passwd;
				
                # FastCGI 参数
                fastcgi_pass  unix:/var/run/fcgiwrap.socket;
                fastcgi_param SCRIPT_FILENAME /usr/lib/git-core/git-http-backend;
                fastcgi_param GIT_HTTP_EXPORT_ALL "";
                # git 库在服务器上的根目录
                fastcgi_param GIT_PROJECT_ROOT    /home/git;
                fastcgi_param PATH_INFO           $1;
                # 将认证用户信息传递给 fastcgi 程序
                fastcgi_param REMOTE_USER $remote_user;
                # 包涵默认的 fastcgi 参数；
                include       fastcgi_params;
                # 将允许客户端 post 的最大值调整为 100 兆
                client_max_body_size 100M;
        }
```

重启nginx后，在其他机器就可以用http协议访问远程库了，如：
``` bash
$ git clone http://<ip or domainname>/git/sample.git
```
系统会出现对话框，提示输入用户名和密码，输入上面用`htpasswd`命令创建的用户名和密码即可。

## Git协议
顺便介绍下Git协议，这是git专用的一个协议，访问远程库的速度最快，但Git协议缺少用户的鉴权，所以一般只用于下载，默认情况下，通过Git协议不能push代码。
git安装后，已经支持通过Git协议，但需要在仓库的目录下增加一个空文件`git-daemon-export-ok`：
``` bash
# touch /home/git/sample.git/git-daemon-export-ok
```
加了这个文件，表示这个仓库支持通过Git协议访问。

用如下命令启动Git协议的守护进程，默认会监听在9418端口。
``` bash
# git daemon --reuseaddr --base-path=/home/git/ /home/git/
```

然后其他机器就能通过Git协议访问这个远程库了：
``` bash
$ git clone git://<ip or domainname>/sample.git
```
