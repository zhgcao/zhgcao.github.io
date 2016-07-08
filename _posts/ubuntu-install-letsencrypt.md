---
title: Ubuntu 安装 Let's Encrypt 获取免费证书
date: 2016-07-07 12:41:11
tags: [Ubuntu, Let's Encrypt, Certbot, Nginx]
---
以前给自己的网站申请免费证书，要去[StartSSL](https://www.startssl.com/)这里申请，申请的步骤相对比较繁琐，证书到期前还要记得到网站去更新。

最近发现了一个开源的工具：Certbot（原来的名字叫Let's Encrypt），安装以后申请证书相当的方便，证书到期前还能用定时任务自动更新，申请到的证书也能被各大主流浏览器接受。
Certbot的介绍：<https://letsencrypt.org/>
在GitHub上的开源项目地址：<https://github.com/certbot/certbot>

下面以Ubuntu 16.04为例，简要介绍如何安装和使用这个工具。

## 安装
在 Ubuntu 16.04 下，安装非常的容易，执行以下一条命令即可：
``` bash
# apt-get install letsencrypt 
```

## 获取证书
首先要有至少一个域名并指向安装工具的服务器，Certbot在获取证书时会使用80端口来验证域名，所以要先停止Apache、Nginx等web服务器，然后执行以下命令：
``` bash
# letsencrypt certonly
```

然后会出现一个交互的窗口，总共3步：
首先输入一个email地址，用于接收紧急通知和恢复。
第二步选择同意他们的服务条款。
第三步输入要申请的域名，如果有多个域名可以一起申请，多个域名以逗号或空格分开，目前还不支持通配的域名证书。

顺利的话，会看到以下信息：
``` text
IMPORTANT NOTES:
 - If you lose your account credentials, you can recover through
   e-mails sent to <email address>.
 - Congratulations! Your certificate and chain have been saved at
   /etc/letsencrypt/live/<domain>/fullchain.pem. Your cert will expire
   on 2016-10-05. To obtain a new version of the certificate in the
   future, simply run Let's Encrypt again.
 - Your account credentials have been saved in your Let's Encrypt
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Let's
   Encrypt so making regular backups of this folder is ideal.
 - If you like Let's Encrypt, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```

这样就表示申请成功了，申请的域名保存在`/etc/letsencrypt/live/<domain>`目录下（`<domain>`是申请时输入的第一个域名），目录下有4个文件：
``` bash
# ls /etc/letsencrypt/live/<domain>/
cert.pem  chain.pem  fullchain.pem  privkey.pem
```
其中`fullchain.pem`为证书文件，`privkey.pem`为证书的私钥。

## 使用申请的证书
以Nginx为例，把证书配置到Nginx的配置文件即可，如以下：
``` text
server {
    listen       443 ssl;
    server_name  <domain>;
    
    # SSL证书的设置
    ssl on;
    ssl_certificate /etc/letsencrypt/live/<domain>/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/<domain>/privkey.pem;
	
	...
}
```

配置后，重启Nginx，然后就可以通过`https://<domain>`来访问了。

## 更新证书
Certbot申请的证书，有效期只有90天，到期前需要更新证书，可以用如下命令来测试一下更新证书，更新前，也需要先暂停Nginx，更新成功后，再重启Nginx：
``` bash
# letsencrypt renew --dry-run --agree-tos
```
其中`--dry-run`表示这是一个测试，并不真正更新，如果成功则表示可以更新证书，正式更新时把这个选项去掉即可。

看到如下信息就表示更新成功了：
``` text
Congratulations, all renewals succeeded. The following certs have been renewed:
  /etc/letsencrypt/live/<domain>/fullchain.pem (success)
```

## 自动更新证书
可以写一个脚本，将停止Nginx、更新证书、重启Nginx这三步写入脚本，由系统定时自动调用，实现证书的自动更新。

比如在`/usr/bin/`创建一个脚本`cb-renew.sh`，脚本内容可以参考如下：
``` text
#!/bin/sh
systemctl stop nginx
letsencrypt renew --agree-tos
systemctl start nginx
```

保存后，设置脚本为可执行：
``` bash
# chmod +x /usr/bin/cb-renew.sh
```

在`/etc/crontab`文件最后增加一行，如下表示每2个月1号的2点30分，以root用户自动执行更新脚本：
``` text
30 2 1 */2 * root /usr/bin/cb-renew.sh
```

重启系统cron进程：
``` bash
# systemctl restart cron
```

## 其他Linux和Webserver的安装使用方法
Certbot目前还是Beta版本，不同的Linux版本，安装使用方法不太一样，其他情况可以访问：<https://certbot.eff.org/>，选择自己的Webserver和操作系统来了解安装使用方法。
