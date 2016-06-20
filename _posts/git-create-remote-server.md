---
title: Git自建远程仓库
date: 2016-06-14 10:34:23
tags: Git
---
用Git做代码控制时，除了放在GitHub这类公开的远程仓库，还可以很方便的自己建个远程仓库玩。

## 准备
找一台Linux机器，以Ubuntu为例，先添加一个git用户，供其他电脑访问远程库：
``` bash
$ sudo adduser git
```

如果是个人使用或小范围团队使用，通过SSH来访问会比较简单，收集所有需要访问的用户的ssh pub key，写入 `/home/git/.ssh/authorized_keys`，一行一条。

为了安全，也可以限制 git 用户登录 shell，编辑 `/etc/passwd`，找到以下内容：
``` text
git:x:1001:1001:,,,:/home/git:/bin/bash
```
把 `/bin/bash` 修改为 `/usr/bin/git-shell`
``` text
git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
```
git-shell一登录就会立即退出。

## 新建远程仓库
远程仓库通常只需要做版本控制，不需要在上面直接修改提交代码，因此可以创建一个裸仓库，裸仓库没有工作区。
找一个放仓库的目录，如 `/home/git`，在这个目录下执行以下命令：
``` bash
$ sudo git init --bare sample.git
```
sample可以根据实际修改为自己想要的名称，git远程仓库往往以`.git`为后缀，上面的命令会建立一个空的仓库，目录名为sample.git。

把目录的所有者改为git
``` bash
$ sudo chown -R git:git sample.git
```

至此远程仓库就建好了，在其他电脑，可以直接clone这个仓库：
``` bash
$ git clone git@<remote server ip>:/home/git/sample
```

## 一个本地库关联多个远程库
公司正式项目的远程仓库需要做每日构建，对提交的代码有一定的要求，必须要功能完备没有编译错误才能提交，同时还限制了在远程仓库上创建分支。如果有个功能需要多人来协作开发，就可以用多个远程库的方式，在功能还未开发完时在团队内同步代码。
自建的远程库可以克隆自项目原来的库，如执行以下命令：
``` bash
$ git clone --bare -b <branch> --depth 1 <project url> [<local dir>]
```
和上面新建一样，`--bare`表示这是一个没有工作区的裸仓库。
如果项目库有多个分支，`-b <branch>` 表示只克隆某个分支，不需要将全库克隆下来。
`--depth 1` 表示是浅克隆，只克隆最新的变更记录，其他不克隆。
`<project url>` 是项目远程仓库的地址，`<local dir>` 为本地目录，可选。

克隆后，同样把所有者改为git用户
``` bash
$ sudo chown -R git:git <local dir>
```

团队成员，在自己机器的本地库上，添加新的远程库：
``` bash
$ git remote add origin2 git@<team server ip>:/home/git/sample
```
远程库的名称默认是origin，为了和原有的远程库区分开，这里把新增的远程库命名为origin2

添加后，这个本地库就关联了2个远程库，代码可以先提交到团队自己的远程库，其他人就能通过拉取这个远程仓库及时获取他人的开发内容，待功能全部完成后再一次性提交到项目正式的远程库。
从新建的远程库拉取和提交修改：
``` bash
$ git pull origin2 master
$ git push origin2 master
```