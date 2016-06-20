---
title: Ubuntu安装net-speeder
date: 2016-05-26 08:37:10
tags: [Ubuntu, Shadowsocks, net-speeder, BandwagonHost]
---
## 介绍
目前个人最主要的科学上网工具，就是部署在搬瓦工上的Shadowsocks了，一直能稳定使用，唯一的缺点是速度不够快，看youtube时经常需要中断缓冲一会，不是很爽。

最近把搬瓦工上的系统更换了一下，重装了Shadowsocks，顺便研究了一下如何加速。

目前在VPS上，比较常用的加速软件有3钟：锐速、FinalSpeed、net-speeder

1. 锐速是国内某公司出的商业软件，不开源，网上流传有破解版可用，但是，它只支持KVM架构的VPS，而我的廉价搬瓦工是OpenVZ架构的，不支持。
2. FinalSpeed是开源的，项目地址：[FinalSpeed](https://github.com/d1sm/finalspeed "Link to FinalSpeed")，这个工具在使用时还需要装个客户端，否则没有任何加速效果，使用上不太方便。更关键的是，跑起来据说占用内存较大（可能因为是用JAVA开发的），作者特别提示搬瓦工至少需要256内存以上，而我的小VPS只有128M内存。
3. net-speeder也是开源的，项目地址：[net-speeder](https://github.com/snooda/net-speeder "Link to net-speeder")，这个项目是用C写的，代码非常简单，只有100多行，加速的原理简单粗暴，就是所有满足条件的包全部发2遍，这种做法有点损人利己，会占用更多带宽并使得流量翻倍，不过在高丢包高延迟环境下会比较有效。

受VPS的限制，可选的加速工具只有net-speeder。

## 安装
目前在VPS上装的系统是Ubuntu 15.10，在Ubuntu下安装net-speeder还是比较简单的。

首先下载net-speeder的源码并解压，解压后有个路径net-speeder-master
``` bash
# wget https://github.com/snooda/net-speeder/archive/master.zip
# unzip master.zip
# cd net-speeder-master
```

如果解压时提示unzip命令没找到，需要先安装unzip
``` bash
# apt-get install unzip
```

准备编译的环境，安装几个依赖的包
``` bash
# apt-get install libnet1-dev
# apt-get install libpcap0.8-dev
```

编译，因为VPS是OpenVZ架构的，需要加-DCOOKED参数，KVM的不用加
``` bash
# sh build.sh -DCOOKED
```
编译后，当前目录下会有个net_speeder的可执行文件。

## 使用
执行net-speeder的命令格式：`./net_speeder 网卡名 加速规则（bpf规则）`

网卡名可以用ifconfig命令看到，我的VPS上显示有2个网卡名：venet0 和 venet0:0，从IP地址看，后面一个绑定的是实际的VPS外网IP。

加速规则实际是个bpf规则，用来过滤哪些包需要发2遍，要了解更详细的bpf规则可以google一下。

项目的作者推荐的加速规则如下，重发所有的ip包：
``` bash
# ./net_speeder venet0:0 "ip"
```

网上很多人推荐的加速规则如下，重发所有的tcp包：
``` bash
# ./net_speeder venet0:0 "tcp"
```

但这两种规则范围还是太大了，尤其是作者推荐的，把所有的ip包全部发2遍，这么玩感觉有点耍流氓了。

而我的目的只需要给Shadowsocks加速，可以设定只加速Shadowsocks监听的端口，假设端口是8388，只加速这个端口的命令如下：
``` bash
# ./net_speeder venet0:0 "tcp src port 8388"
```

假如需要给多个端口加速，命令可以写成：
``` bash
# ./net_speeder venet0:0 "tcp src port 8388 or port 8389"
```

以后台方式执行：
``` bash
# nohup ./net_speeder venet0:0 "tcp src port 8388" >/dev/null 2>&1 &
```

关闭net-speeder
``` bash
# killall net_speeder
```

把这个工具加入开机启动，先拷贝到/usr/bin目录下
``` bash
# cp ./net_speeder /usr/bin
echo 'nohup /usr/bin/net_speeder venet0:0 "tcp src port 8388" >/dev/null 2>&1 &' >> /etc/rc.local
```

## 效果
没详细测试加速前后下载等速度的差异，从youtube来看，效果还是非常明显的，youtube统计信息里的速度有了非常大的提升，缓存进度条跑的快了很多，一般视频都不会中断了。