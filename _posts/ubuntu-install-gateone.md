---
title: Ubuntu 安装配置 GateOne
date: 2016-09-06 19:03:29
tags: [Ubuntu, GateOne, Nginx]
---
平常要操作VPS，大多是用putty这样的SSH客户端连接，但某些情况，不能直接通过SSH连接，putty就无法使用了。
最近试用了一下GateOne，这是一个Web方式的SSH，安装设置好后，就可以通过Web页面来访问VPS了，试用下来，还是挺好用的。

GateOne是开源的，在GitHub上的地址：<https://github.com/liftoff/GateOne>
官方的介绍：<http://liftoff.github.io/GateOne/>

## 安装
GateOne是用Python基于tornado框架开发的，要安装GateOne，需要先安装python、pip、tornado。
python、pip的安装略过，tornado可以用如下命令安装：
``` bash
# pip install tornado
```

找一个目录，从GitHub下载GateOne的代码并安装（需要root权限）：
``` bash
# git clone git://github.com/liftoff/GateOne
# cd GateOne/
# python setup.py install
```

## 启动
安装完成后，直接执行如下命令就能启动GateOne（如果有Nginx、Apache等web服务器占用了443端口，需要先停止）：
``` bash
# gateone
```
如果要以后台方式启动，可以用如下命令：
``` bash
# service gateone start
```

执行后，在浏览器输入`https://<VPS IP>` 就能使用了。

## 配合Nginx的设置
虽然GateOne并不需要设置就能用，但其自带的SSL证书是无效的，访问时会提示网站不可信，同时，可能也不希望其独占443端口，这时可以配合Nginx来使用。

先修改GateOne的配置文件：`/etc/gateone/conf.d/10server.conf`
找到以下几项内容：
``` text
"address": "127.0.0.1",		
"disable_ssl": true,
"port": 54321,
"url_prefix": "/gateone/",
```
address改为"127.0.0.1"，这样外网不能直接访问GateOne，只能通过Nginx转发
disable_ssl设为True，表示不用GateOne自带的证书
port改为一个未占用的端口，要和Nginx设置一致
url_prefix改为"/gateone/"，要和Nginx设置一致

修改Nginx的设置文件，如`/etc/nginx/sites-enabled/default`，设置示例如下：
``` text
server {
    listen       443 ssl;
    server_name  <domain>;
	
	ssl on;
    ssl_certificate /etc/letsencrypt/live/<domain>/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/<domain>/privkey.pem;	

    # GateOne
    location /gateone/ {
        proxy_pass       http://127.0.0.1:54321;

        proxy_redirect off;
        proxy_pass_header Server;
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $http_address;
        proxy_set_header X-Scheme $scheme;

        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }	
```
其中的location和proxy_pass中的端口，要和GateOne中的设置一致。

设置好后，重启Nginx和GateOne，然后在浏览器输入`https://<Domain>/gateone/` 就能使用了。

## 使用
GateOne用起来很方便，基本试试就会了，功能也比较强大，一个页面还可以最多开4个窗口，另外，还支持直接查看图片、pdf等文件，这点比putty还方便。
