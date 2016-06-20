---
title: 利用Nginx反向代理谷歌
date: 2016-06-09 11:23:56
tags: [Nginx, Google]
---
自从谷歌被墙，虽然在家可以翻墙访问，但在公司翻墙不方便，只能在网上找谷歌镜像站（镜像站是通俗叫法，更准确的叫法是反向代理）。但这些找来的镜像站往往不太稳定，经常过一段时间就不能访问甚至也会被墙，所以想着要搭个个人自用的反向代理。

## 搭反向代理需要的条件
+ 一个墙外的VPS
  对VPS的要求不高，搬瓦工128m内存的小机，搭建及使用均毫无压力。
  
+ 一个域名
  网上有很多地方可以申请免费的域名，比如[Freenom](http://www.freenom.com/)，可以免费申请.tk、.ml、.cf、.ga等后缀的顶级域名，最长一年免费，到期可以免费续期。
  
+ 域名的SSL证书
  其实反向代理本身是不需要SSL证书的，但如果没有证书只用http，仍然会被墙检测到搜索的内容，也就非常容易被拦截甚至被封IP，所以，反代谷歌最好有SSL证书，用https协议来访问。
  网上能免费申请SSL证书的地方不多，推荐[StartSSL](https://www.startssl.com/)，也是最长一年免费，到期再免费续期。申请SSL证书的步骤比较麻烦，可以看看这个教程：[新StartSSL免费SSL证书申请使用](http://www.freehao123.com/startssl-ssl-apache-ngnix/)。
  
## 安装并编译Nginx
Nginx自身就支持反向代理功能，但为了更好的反代谷歌，还需要至少一个第三方模块，Nginx加入第三方模块相对有点麻烦，需要下载源码重新编译。
这次折腾编译Nginx，参考了些网上的教程，但没有完全按照教程，步骤稍有点多，主要想尽量保留原有发行版的功能，以备以后用Nginx做其他用途。

以下命令全是在Ubuntu上执行，CentOs的命令不一样，用户默认为root，非root用户很多命令需要加sudo。

先安装需要用到的一些包
``` bash
# apt-get update
# apt-get install libpcre3 libpcre3-dev
# apt-get install zlib1g zlib1g-dev openssl libssl-dev
# apt-get install libxml2 libxml2-dev libxslt1-dev
# apt-get install libgd-dev libgeoip-dev
# apt-get install -y gcc g++ make automake
```

安装Nginx的发行版
``` bash
# apt-get install nginx
```

查看发行版的版本号和编译参数
``` bash
# nginx -V
nginx version: nginx/1.9.3 (Ubuntu)
built with OpenSSL 1.0.2d 9 Jul 2015
TLS SNI support enabled
configure arguments: --with-cc-opt='-g -O2 -fPIE -fstack-protector-strong -Wformat -Werror=format-security -D_FORTIFY_SOURCE=2' --with-ld-opt='-Wl,-Bsymbolic-functions -fPIE -pie -Wl,-z,relro -Wl,-z,now' --prefix=/usr/share/nginx --conf-path=/etc/nginx/nginx.conf --http-log-path=/var/log/nginx/access.log --error-log-path=/var/log/nginx/error.log --lock-path=/var/lock/nginx.lock --pid-path=/run/nginx.pid --http-client-body-temp-path=/var/lib/nginx/body --http-fastcgi-temp-path=/var/lib/nginx/fastcgi --http-proxy-temp-path=/var/lib/nginx/proxy --http-scgi-temp-path=/var/lib/nginx/scgi --http-uwsgi-temp-path=/var/lib/nginx/uwsgi --with-debug --with-pcre-jit --with-ipv6 --with-http_ssl_module --with-http_stub_status_module --with-http_realip_module --with-http_auth_request_module --with-http_addition_module --with-http_dav_module --with-http_geoip_module --with-http_gzip_static_module --with-http_image_filter_module --with-http_spdy_module --with-http_sub_module --with-http_xslt_module --with-mail --with-mail_ssl_module
```

从上面的信息可以看到当前安装的是1.9.3版本，configure arguments后面跟的一长串是发行版的编译参数，很重要，后面需要用到。

找一个目录放源码，在这个目录里下载对应版本的Nginx源码并解压：
``` bash
# wget http://nginx.org/download/nginx-1.9.3.tar.gz
# tar -zxvf nginx-1.9.3.tar.gz
```

为了在反代时，更好的替换原网页中的信息，需要在编译时增加一个第三方模块：[substitutions 扩展](https://github.com/yaoweibin/ngx_http_substitutions_filter_module)
下载 substitutions 的源码
``` bash
# git clone https://github.com/yaoweibin/ngx_http_substitutions_filter_module
```
下载后，会在当前目录下有个 ngx_http_substitutions_filter_module 目录

另一个用于便捷配置反代Google的第三方模块：[ngx_http_google_filter_module](https://github.com/cuber/ngx_http_google_filter_module)
``` bash
git clone https://github.com/cuber/ngx_http_google_filter_module
```
下载后，会在当前目录下有个 ngx_http_google_filter_module 目录，这个模块可选，增加后在Nginx配置文件中可以很简单的反代google

进入Nginx源码的目录，设置编译参数，其实就是在原发行版的编译参数后，增加两个` --add-module=../xxx`，把两个第三方模块包括进来。
``` bash
# cd nginx-1.9.3
# ./configure \
> --with-cc-opt='-g -O2 -fPIE -fstack-protector-strong -Wformat -Werror=format-security -D_FORTIFY_SOURCE=2' --with-ld-opt='-Wl,-Bsymbolic-functions -fPIE -pie -Wl,-z,relro -Wl,-z,now' --prefix=/usr/share/nginx --conf-path=/etc/nginx/nginx.conf --http-log-path=/var/log/nginx/access.log --error-log-path=/var/log/nginx/error.log --lock-path=/var/lock/nginx.lock --pid-path=/run/nginx.pid --http-client-body-temp-path=/var/lib/nginx/body --http-fastcgi-temp-path=/var/lib/nginx/fastcgi --http-proxy-temp-path=/var/lib/nginx/proxy --http-scgi-temp-path=/var/lib/nginx/scgi --http-uwsgi-temp-path=/var/lib/nginx/uwsgi --with-debug --with-pcre-jit --with-ipv6 --with-http_ssl_module --with-http_stub_status_module --with-http_realip_module --with-http_auth_request_module --with-http_addition_module --with-http_dav_module --with-http_geoip_module --with-http_gzip_static_module --with-http_image_filter_module --with-http_spdy_module --with-http_sub_module --with-http_xslt_module --with-mail --with-mail_ssl_module \
> --add-module=../ngx_http_substitutions_filter_module \
> --add-module=../ngx_http_google_filter_module
```

设置后，会开始检查这些编译参数和环境，如果上面少安装了某些包，或更新的版本需要其他的包，就会报错，如少安装了libgeoip-dev，会报如下错误：
``` text
./configure: error: the GeoIP module requires the GeoIP library.
```
如果报了这类错误，可以Google一下错误信息，找到缺失的包并安装，然后再次执行 `./configure ...` 命令设置编译参数。

如果检查通过，最后会显示如下的信息：
``` text
Configuration summary
  + using system PCRE library
  + using system OpenSSL library
  + md5: using OpenSSL library
  + sha1: using OpenSSL library
  + using system zlib library

  nginx path prefix: "/usr/share/nginx"
  nginx binary file: "/usr/share/nginx/sbin/nginx"
  nginx configuration prefix: "/etc/nginx"
  nginx configuration file: "/etc/nginx/nginx.conf"
  nginx pid file: "/run/nginx.pid"
  nginx error log file: "/var/log/nginx/error.log"
  nginx http access log file: "/var/log/nginx/access.log"
  nginx http client request body temporary files: "/var/lib/nginx/body"
  nginx http proxy temporary files: "/var/lib/nginx/proxy"
  nginx http fastcgi temporary files: "/var/lib/nginx/fastcgi"
  nginx http uwsgi temporary files: "/var/lib/nginx/uwsgi"
  nginx http scgi temporary files: "/var/lib/nginx/scgi"
```

然后编译Nginx
``` bash
# make 
# make install
```

将编译后的文件替换到发行版的安装目录
``` bash
# cp -rf objs/nginx /usr/sbin/nginx
```

可以用如下命令来关闭/启动 Nginx
``` bash
# systemctl stop nginx
# systemctl start nginx
```

用如下命令可以查看运行的状态
``` bash
# systemctl status nginx
```

## Nginx的设置
Nginx的配置文件是 `/etc/nginx/nginx.conf`，贴一下我反代Google的设置：
``` text
    # upstream配置google的ip，ip可以通过 nslookup www.google.com 命令获取，
    # 多运行几次nslookup会获取到多个IP，有助于避免触发google的防机器人检测。
    upstream www.google.com {
        server 172.217.0.4:443 weight=1;
        server 172.217.1.36:443 weight=1;
        server 216.58.193.196:443 weight=1;
        server 216.58.216.4:443 weight=1;
        server 216.58.216.36:443 weight=1;
        server 216.58.219.36:443 weight=1;
        server 74.125.25.99:443 weight=1;
        server 74.125.25.103:443 weight=1;
        server 74.125.25.104:443 weight=1;
        server 74.125.25.105:443 weight=1;
        server 74.125.25.106:443 weight=1;
        server 74.125.25.147:443 weight=1;
    }

    # 这里将http的访问强制跳转到https，<domain.name>改为自己的域名。
    server { 
        listen 80;
        server_name <domain.name>;
        # http to https
        location / {
              rewrite ^/(.*)$ https://<domain.name>$1 permanent;
        }
    }
    
    # https的设置
    server {
        listen       443 ssl;
        server_name  <domain.name>;
        resolver 8.8.8.8;
        
        # SSL证书的设置，<path to ssl.xxx>改为自己的证书路径
        ssl on;
        ssl_certificate <path to ssl.crt>;
        ssl_certificate_key <path to ssl.key>;

        # 防止网络爬虫
        #forbid spider
        if ($http_user_agent ~* "qihoobot|Baiduspider|Googlebot|Googlebot-Mobile|Googlebot-Image|Mediapartners-Google|Adsbot-Google|Feedfetcher-Google|Yahoo! Slurp|Yahoo! Slurp China|YoudaoBot|Sosospider|Sogou spider|Sogou web spider|MSNBot|ia_archiver|Tomato Bot") 
        { 
            return 403; 
        }

        # 禁止用其他域名或直接用IP访问，只允许指定的域名
        #forbid illegal domain
        if ( $host != "<domain.name>" ) {
            return 403; 
        }

        access_log  off;
        error_log   on;
        error_log  /var/log/nginx/google-proxy-error.log;
	
        # 编译时加了 ngx_http_google_filter_module 模块，location的设置就非常简单
        location / {
            google on;
        }
    }
```

如果编译时没有加 ngx_http_google_filter_module 模块，location的设置可以参考如下：
``` text
        location / {
            proxy_redirect off;
            proxy_cookie_domain google.com <domain.name>; 
            proxy_pass https://www.google.com;
            proxy_connect_timeout 60s;
            proxy_read_timeout 5400s;
            proxy_send_timeout 5400s;

            proxy_set_header Host "www.google.com";
            proxy_set_header User-Agent $http_user_agent;
            proxy_set_header Referer https://www.google.com;
            proxy_set_header Accept-Encoding "";
            proxy_set_header X-Real-IP $remote_addr; 
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; 
            proxy_set_header X-Forwarded-Proto https;
            proxy_set_header Accept-Language "zh-CN";
            proxy_set_header Cookie "PREF=ID=047808f19f6de346:U=0f62f33dd8549d11:FF=2:LD=en-US:NW=1:TM=1325338577:LM=1332142444:GM=1:SG=2:S=rE0SyJh2W1IQ-Maw";

            subs_filter https://www.google.com.hk <domain.name>;
            subs_filter https://www.google.com <domain.name>;
            #subs_filter_types text/css text/xml text/javascript;

            sub_filter_once off; 
        }
```

修改完配置文件后，可以用 `nginx -t` 检查配置文件是否正确：
``` bash
# nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```
看到上面的显示就表明设置正确。

配置文件修改后需要重启Nginx：
``` bash
# systemctl restart nginx
```
