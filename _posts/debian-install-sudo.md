---
title: Debian安装sudo
date: 2016-05-13 11:28:09
tags: Debian
---
以前用Ubuntu时，sudo可以说是一个非常常用的命令，最近试用Debian，用非root用户登录，执行`sudo apt-get install ...`时，居然提示：sudo: command not found

然后用`dpkg -l|grep sudo`查看，发现默认就没有安装sudo，只能自己装了：

先su到root用户，然后执行 `apt-get install sudo`

安装好后，执行 `visudo`，编辑允许使用sudo的用户，找到如下内容
``` text
# User privilege specification
root    ALL=(ALL:ALL) ALL
```

在下面新增一行，允许其他用户使用sudo，&lt;user&gt;是实际的用户名：
``` text
<user>   ALL=(ALL:ALL) ALL
```

按Ctrl-O保存文件，注意默认保存的文件名是/etc/sudoers.tmp，需要把.tmp删除，直接保存为/etc/sudoers，会提示是否覆盖，选择Yes。
然后按Ctrl-X退出编辑。

也可以用vi直接编辑/etc/sudoers文件，这个文件是只读的，保存时需要用 :wq!

修改后，退出su，这样，其他用户就能使用sudo了。
