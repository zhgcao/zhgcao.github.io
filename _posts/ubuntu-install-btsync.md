---
title: Ubuntu安装BTSync
date: 2016-04-30 14:25:12
tags: [Ubuntu, BTSync]
---
[BTSync](https://www.getsync.com/)是多台电脑之间同步文件的利器，很好用。

在Ubuntu上安装BTSync的步骤：

### 添加btsync官方的Repository
以下命令新建一个 /etc/apt/sources.list.d/btsync.list 文件。
``` shell
sudo sh -c 'echo "deb http://linux-packages.getsync.com/btsync/deb btsync non-free" > /etc/apt/sources.list.d/btsync.list'
```

### 安装BTSync的Public key
使Ubuntu信任此BTSync提供的Repository。
``` shell
wget -qO - http://linux-packages.getsync.com/btsync/key.asc | sudo apt-key add -
```

### 安装
接下来就能用apt-get安装BTSync了，需要先update一下。
``` shell
sudo apt-get update
sudo apt-get install btsync
```

### 创建用于同步的目录
安装默认创建了一个btsync用户，可以创建一个新目录并授权给btsync用户，以后同步的内容就放在这个目录下。
``` shell
sudo mkdir /home/btsync
sudo chown btsync /home/btsync
```

### 使BTSync自动启动
默认启动服务的用户是btsync
``` shell
sudo systemctl enable btsync
```

除了enable，systemctl命令还可以带disable、start、stop、status等参数。

### 通过web设置BTSync
在本地用浏览器访问localhost:8888即可。

如果是VPS或Ubuntu server等没有图形界面的系统，可以编辑BTSync的配置文件。
``` shell
sudo vi /etc/btsync/config.json
```

找到下面内容：
``` text
    "webui" :
    {
        "listen" : "127.0.0.1:8888"
    }
```
将 "127.0.0.1:8888" 修改为 "0.0.0.0:8888" ，保存退出。重启BTSync。
然后就能用其他机器的浏览器访问 "Ubuntu_IP:8888" 来设置BTSync。


### 参考链接
[Installing Sync On Linux](http://help.getsync.com/hc/en-us/articles/207689576-Installing-Sync-on-Linux)