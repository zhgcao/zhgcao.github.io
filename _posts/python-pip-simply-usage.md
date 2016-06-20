---
title: python pip的简单用法
date: 2016-05-03 17:17:52
tags: [python, pip]
---
pip是一个以Python 语言写成的软件包管理系统，可以安装和管理软件包。

### 安装pip
Windows环境下，Python2.7.9以及3.4以后的版本，已经自带pip，默认在安装Python时就会自动安装。

在Ubuntu15下，默认安装的是Python3，如果要安装对应版本的pip，执行如下命令：
``` shell
$ sudo apt-get install python3-pip
```

如果执行如下命令，会连带安装Python2.7版本：
``` shell
$ sudo apt-get install python-pip
```

### 查看帮助
命令**help**，格式如下：
``` shell
>pip help
```

查看某条命令的帮助，如：
``` shell
>pip help install
```

### 安装软件包
命令**install**，格式如下：
``` shell
>pip install xlwt
```

更新某个软件包，如更新pip自身：
``` shell
>pip install --upgrade pip
```
--upgrade 可以简化写为 -U

如果要通过代理才能访问网络，pip命令可以加参数`--proxy=http://<proxy_address>:<proxy_port>`

### 卸载已安装的软件包
命令**uninstall**，格式如下：
``` shell
>pip uninstall xlwt
```

### 查看已经安装的包
命令**list**，格式如下：
``` shell
>pip list
```

结果显示如下：
``` shell
C:\Users\admin>pip list
xlrd (0.9.4)
xlutils (1.7.1)
xlwt (1.0.0)
```

检查哪些包需要更新，如：
``` shell
>pip list --outdated
```
--outdated 可以简化写为 -o

### 显示包的详细信息
命令**show**，格式如下：
``` shell
>pip show xlwt
```

结果显示如下：
``` shell
C:\Users\admin>pip show xlwt
---
Metadata-Version: 2.0
Name: xlwt
Version: 1.0.0
......
```

可以带个 --files 参数，显示包里的文件
``` shell
>pip show --files xlwt
```
--files 可以简化写为 -f

### 搜索
命令**search**，格式如下：
``` shell
>pip search xlwt
```

结果显示如下，：
``` shell
C:\Users\admin>pip search xlwt
xlutils (1.7.1)      - Utilities for working with Excel files that require
                       both xlrd and xlwt
  INSTALLED: 1.7.1 (latest)
......
```
显示内容包含了符合搜索条件的包的名称、版本和简要介绍，如果搜索的包已经安装，会显示当前安装的版本。

