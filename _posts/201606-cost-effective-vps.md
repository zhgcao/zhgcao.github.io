---
title: 性价比较高的VPS（201606）
date: 2016-07-03 09:32:14
tags: [VPS, BandwagonHost, HostOdo, HostUs]
---
原来用的VPS是在搬瓦工特价时申请的，只有128m内存，4G硬盘，虽然用来爬墙是足够了，但如果要折腾点别的，就觉得还是小了点，所以一直想着再申请个大一点的。正好最近也有两个朋友问我关于VPS的事，就去了解了一下。

## 搬瓦工 (BandwagonHost)
[搬瓦工](https://bandwagonhost.com/ "Link to BandwagonHost") 是我申请第一个VPS的地方，当时特价的时候很便宜，我的128m内存的，一年只需要6刀，而且一直很稳定，没出过啥问题。不过现在特价的没有了，最便宜的要20刀一年，而且这个20刀的只有256m内存，要知道当年特价的时候，512m的也只要10刀。

虽然价钱贵了，不过搬瓦工仍然是值得推荐的，一是他的服务一直比较稳定，算是一个比较老牌可靠的VPS提供商，二是他们的管理面板上可以一键安装Shadowsocks，对于国内买VPS用来爬墙的算是个很贴心的服务。

目前两款比较低配的OpenVZ的VPS参数如下：

10G VPS | 20G VPS
:-----|:-----
价格: $19.99/年  | 价格: $49.99/年
硬盘: 10 GB SSD  | 硬盘: 20 GB SSD
内存: 256 MB  | 内存: 512 MB 
CPU: 1x Intel Xeon | CPU: 2x Intel Xeon
带宽: 500 GB/月 | 带宽: 1 TB/月 
[购买链接](https://bandwagonhost.com/aff.php?aff=1322&pid=12) | [购买链接](https://bandwagonhost.com/aff.php?aff=1322&pid=13)

点击购买链接后，默认是月付的，要年付，需要在 Billing Cycle 中改为 "$x9.99 USD Annually"。
搬瓦工现在有5个机房可选，美国的洛杉矶、费利蒙、凤凰城、佛罗里达和荷兰的阿姆斯特丹，购买后也可以在5个机房之间随意转移VPS，还支持支付宝付款。

## HostUs
[HostUs](https://hostus.us/ "Link to HostUs") 据说也是一个开了挺多年的VPS提供商，低价的VPS内存和流量都挺大的，而且在亚洲的香港和新加坡也有机房。

在HostUs购买VPS需要注意一点：注册时的IP、填的地址和电话，都要一致，如果翻墙注册时填国内的地址电话，很容易被认为是欺诈而取消掉。

个人觉得以下几个OpenVZ架构的VPS性价比不错：

768MB | 2 GB | 亚太 256MB | 亚太 512MB
:-----|:-----|:-----|:-----
价格: $15/年 | 价格: $35/年 | 价格: $25/年 | 价格: $35/年
内存: 768 MB  | 内存: 2 GB  | 内存: 256 MB  | 内存: 512 MB
vSwap: 768 MB  | vSwap: 2 GB  | vSwap: 256 MB  | vSwap: 512 MB
硬盘: 20 GB  | 硬盘: 75 GB | 硬盘: 10 GB SSD | 硬盘: 15 GB SSD
CPU: 1x | CPU: 4x | CPU: 1x | CPU: 1x
带宽: 2 TB/月 | 带宽: 2 TB/月 | 带宽: 500 GB/月 | 带宽: 750 GB/月
机房：美国洛杉矶、亚特兰大、达拉斯、华盛顿和英国伦敦 | 机房：美国洛杉矶、亚特兰大、达拉斯、华盛顿和英国伦敦 | 机房：香港、新加坡 | 机房：香港、新加坡
[购买链接](https://my.hostus.us/aff.php?aff=1361&pid=103) | [购买链接](https://my.hostus.us/aff.php?aff=1361&pid=142) | [购买链接](https://my.hostus.us/aff.php?aff=1361&pid=183) | [购买链接](https://my.hostus.us/aff.php?aff=1361&pid=179) | 

上面的几个都是OpenVZ架构的，如果需要KVM架构的，可以考虑下面两个：

KVM-0.5 | KVM-1
:-----|:-----
价格: $4.35/月  | 价格: $7.95/月
内存: 512 MB  | 内存: 1 GB 
硬盘: 15 GB SSD  | 硬盘: 30 GB SSD
带宽: 750 GB/月 | 带宽: 1 TB/月 
机房：美国洛杉矶、亚特兰大、华盛顿 | 机房：美国洛杉矶、亚特兰大、华盛顿
[购买链接](https://my.hostus.us/aff.php?aff=1361&pid=307) | [购买链接](https://my.hostus.us/aff.php?aff=1361&pid=308)

## Hostodo
[Hostodo](https://hostodo.com/) 据说是一家2014年才开的VPS提供商，用的人似乎还不多，低价的VPS硬盘和流量比较大，似乎用来做个人网盘挺好的，不过因为开的时间还不够长，稳定性未知，如果做网盘建议做好备份。

个人觉得以下几个OpenVZ的VPS性价比不错：

VZ-256| VZ-512 | VZ-1000
:-----|:-----|:-----
价格: $10/年 | 价格: $12/年 | 价格: $18/年
内存: 256 MB  | 内存: 512 MB  | 内存: 1 GB
vSwap: 256 MB  | vSwap: 512 MB  | vSwap: 1 GB
硬盘: 40 GB  | 硬盘: 55 GB | 硬盘: 90 GB
带宽: 1 TB/月 | 带宽: 2 TB/月 | 带宽: 3 TB/月
优惠码: 无 | 优惠码: LETYRLYSPECIAL512M | 优惠码: LowEnd1G
购买链接：<br/>[洛杉矶](http://hostodo.com/portal/aff.php?aff=162&pid=12)，[迈阿密](http://hostodo.com/portal/aff.php?aff=162&pid=42)，[达拉斯](http://hostodo.com/portal/aff.php?aff=162&pid=89) | 购买链接：<br/>[洛杉矶](http://hostodo.com/portal/aff.php?aff=162&pid=13)，[迈阿密](http://hostodo.com/portal/aff.php?aff=162&pid=43)，[达拉斯](http://hostodo.com/portal/aff.php?aff=162&pid=90) | 购买链接：<br/>[洛杉矶](http://hostodo.com/portal/aff.php?aff=162&pid=14)，[迈阿密](http://hostodo.com/portal/aff.php?aff=162&pid=44)，[达拉斯](http://hostodo.com/portal/aff.php?aff=162&pid=91) 

注意VZ-512和VZ-1000都要优惠码，直接点链接默认是月付的，在 Billing Cycle 选择年付后默认的价格分别是$25/年和$45/年，要点击Continue后，找到“Have a promotion code? Click here to add it”，点击输入上面的优惠码才会变成$12/年和$18/年。

Hostodo还有个卖点，就是号称可以提供亚洲优化IP，见[官方公告](http://hostodo.com/portal/announcements/13/Asia-Optimized-IPs-Available.html)，如果有需求，可以发Ticket或邮件给他们申请。不过用网上看到的测试IP来测试Ping值，速度的提升很有限，仅比原IP稍好一点。