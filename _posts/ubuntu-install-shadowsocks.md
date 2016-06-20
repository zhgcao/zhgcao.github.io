---
title: Ubuntu安装Shadowsocks
date: 2016-05-10 14:31:35
tags: [Ubuntu, Shadowsocks]
---
Shadowsocks可以说是我目前用过的最好的科学上网工具了，没有之一，从大约一年半前在搬瓦工上安装好Shadowsocks以来，这么长时间一直能稳定使用。

搬瓦工上默认的系统是CentOS，以前在这上面装的Shadowsocks，最近计划把系统换成Ubuntu，先学习了解一下Ubuntu下如何安装Shadowsocks。

Shadowsocks有多个版本，常用的有Libev、Python、Go等，以前在CentOS上装的是Libev版本，这次想试试Python版，Python版本可以通过配置文件同时打开多个端口。

**注意以下几乎所有的命令都需要在root用户下执行，如果非root用户，需要在每条命令前加上`sudo `。**

## 安装
Ununtu下安装Python版本的Shadowsocks其实挺简单，先执行如下命令安装pip和几个依赖的包：
``` bash
# apt-get install python-pip python-gevent python-m2crypto
```

可能会顺带安装其他的一堆依赖的包，等全部安装完成后，就可以用pip来安装Shadowsocks：
``` bash
# pip install shadowsocks
```
注意非root用户在命令前一定要加sudo，否则只会安装在用户的home目录下。

安装成功的结果显示如下：
``` bash
Downloading/unpacking shadowsocks
  Downloading shadowsocks-2.8.2.tar.gz
  Running setup.py (path:/tmp/pip-build-4KPgyg/shadowsocks/setup.py) egg_info for package shadowsocks

Installing collected packages: shadowsocks
  Running setup.py install for shadowsocks

    Installing sslocal script to /usr/local/bin
    Installing ssserver script to /usr/local/bin
Successfully installed shadowsocks
Cleaning up...
```
可以看到，默认ssserver和sslocal这两个程序安装到了/usr/local/bin目录

## 设置
执行下面命令在/etc目录下建立一个新目录：shadowsocks，并在其下创建一个文件：config.json：
``` bash
# mkdir /etc/shadowsocks
# vim /etc/shadowsocks/config.json
```

config.json的内容如下：
``` text
{
    "server":"0.0.0.0",
    "server_port":8388,
    "password":"your_password",
    "timeout":600,
    "method":"aes-256-cfb",
    "fast_open": false
}
```
"server_port"和"password"可以根据自己的要求设定。

如果需要同时开多个端口，config.json的内容可以设置如下：
``` text
{
    "server":"0.0.0.0",
    "port_password": {
        "8388": "your_password1",
        "8389": "your_password2"
    },
    "timeout":600,
    "method":"aes-256-cfb",
    "fast_open": false
}
```

## 运行
执行以下命令启动Shadowsocks服务器：
``` bash
# ssserver -c /etc/shadowsocks/config.json -d start 
INFO: loading config from /etc/shadowsocks/config.json
2016-04-19 03:48:17 INFO     loading libcrypto from libcrypto.so.1.0.0
started
```
命令中的`-d start`参数表示Shadowsocks以后台方式运行，所以需要root权限

然后运行`netstat -anp`可以查看到Shadowsocks正监听在设置的端口。

停止Shadowsocks执行如下命令：
``` bash
# ssserver -c /etc/shadowsocks/config.json -d stop 
```

## 设置为服务随系统启动
执行下面的命令，创建shadowsocks.service文件：
``` bash
# vim /etc/systemd/system/shadowsocks.service
```

文件的内容如下：
``` text
[Unit]
Description=Shadowsocks
After=network.target

[Service]
Type=forking
PIDFile=/run/shadowsocks/server.pid
PermissionsStartOnly=true
ExecStartPre=/bin/mkdir -p /run/shadowsocks
ExecStartPre=/bin/chown root:root /run/shadowsocks
ExecStart=/usr/local/bin/ssserver --pid-file /var/run/shadowsocks/server.pid -c /etc/shadowsocks/config.json -d start
Restart=on-abort
User=root
Group=root
UMask=0027

[Install]
WantedBy=multi-user.target 
```

设置文件权限：
``` bash
# chmod 755 /etc/systemd/system/shadowsocks.service
```

启动服务，enable是将服务设为自动启动。
``` bash
# systemctl start shadowsocks
# systemctl enable shadowsocks
```

查看服务状态：
``` bash
# systemctl status shadowsocks
```
