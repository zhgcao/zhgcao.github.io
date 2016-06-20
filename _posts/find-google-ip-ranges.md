---
title: 如何查找Google的IP地址段
date: 2016-06-01 16:20:09
tags: Google
---
一般查找域名对应的IP，可以用这个命令 `nslookup <域名>`，但这样找，返回的ip比较少，今天看到一条命令，可以批量查找Google当前的IP范围。

命令及返回的结果如下：
``` bash
# nslookup -q=TXT _netblocks.google.com 8.8.8.8
Server:         8.8.8.8
Address:        8.8.8.8#53

Non-authoritative answer:
_netblocks.google.com   text = "v=spf1 ip4:64.18.0.0/20 ip4:64.233.160.0/19 ip4:66.102.0.0/20 ip4:66.249.80.0/20 ip4:72.14.192.0/18 ip4:74.125.0.0/16 ip4:108.177.8.0/21 ip4:173.194.0.0/16 ip4:207.126.144.0/20 ip4:209.85.128.0/17 ip4:216.58.192.0/19 ip4:216.239.32.0/19 ~all"

Authoritative answers can be found from:
```

这条命令是从谷歌的DNS服务器（8.8.8.8），获取很多的IP地址段。每个地址段格式如下：
``` text
216.239.32.0/19
```
这是一个CIDR（无类别域间路由，Classless Inter-Domain Routing），前面的216.239.32.0是起始地址，后面的/19表示掩码的位数，这里具体的掩码是255.255.224.0，在这个掩码范围内的都是Google的IP，总计8190个。掩码位数越小，表示IP地址段的范围越大。上面查到的这些地址段表示的IP，全部加起来大概有二十多万个。

要根据CIDR计算地址范围可以[点这里](http://www.aboutmyip.com/AboutMyXApp/SubnetCalculator.jsp?ipAddress=216.239.32.0&cidr=19)。

另外，VPS上安装的精简版Ubuntu，默认并没有安装nslookup，可以执行以下命令安装：
``` bash
# apt-get install dnsutils
```

Centos安装nslookup的命令如下：
``` bash
# yum install bind-utils
```