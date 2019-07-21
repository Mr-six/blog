---
title: '局域网共享之 NFS'
date: 2019-07-21T18:35:06+08:00
categories: ['linux']
tags: ['网络']
---

> 之前用家里的mini主机搭了一个单机k8s,用来自动帮我下载关注的美剧。省去了每次资源更新了还要手动去下载的烦恼。但是又有了新的烦恼：家里的设备（电视盒子，苹果手机、安卓手机，iPad，电脑）如何去播放放在硬盘的美剧呢？

在折腾过在线播放plex，Samba，NFS之后，最终决定使用NFS作为局域网共享服务。

开始之前首先需要介绍一下 `/etc/fsta` 文件

fstab文件可用于定义磁盘分区，各种其他块设备或远程文件系统应如何装入文件系统。
## `/etc/fsta`示例
```
# <file system>        <dir>    <type>    <options>    <dump> <pass>
UUID=0005B7D20000BFA9  /test     ext4     defaults       0      0
```

## `/etc/fsta`字段定义：


- `<file systems>` - 要挂载的分区或存储设备.
- `<dir>`  `<file systems>`的挂载位置。
- `<type>` 要挂载设备或是分区的文件系统类型，支持许多种不同的文件系统：ext2, ext3, ext4, reiserfs, xfs, jfs, smbfs, iso9660, vfat, ntfs, swap 及 auto。 设置成auto类型，mount 命令会猜测使用的文件系统类型，对 CDROM 和 DVD 等移动设备是非常有用的。
- `<options>` 挂载时使用的参数，注意有些 参数是特定文件系统才有的。一些比较常用的参数有：
    - auto - 在启动时或键入了 mount -a 命令时自动挂载。
    - noauto - 只在你的命令下被挂载。
    - exec - 允许执行此分区的二进制文件。
    - noexec - 不允许执行此文件系统上的二进制文件。
    - ro - 以只读模式挂载文件系统。
    - rw - 以读写模式挂载文件系统。
    - user - 允许任意用户挂载此文件系统，若无显示定义，隐含启用 noexec, nosuid, nodev 参数。
    - users - 允许所有 users 组中的用户挂载文件系统.
    - nouser - 只能被 root 挂载。
    - owner - 允许设备所有者挂载.
    - sync - I/O 同步进行。
    - async - I/O 异步进行。
    - dev - 解析文件系统上的块特殊设备。
    - nodev - 不解析文件系统上的块特殊设备。
    - suid - 允许 suid 操作和设定 sgid 位。这一参数通常用于一些特殊任务，使一般用户运行程序时临时提升权限。
    - nosuid - 禁止 suid 操作和设定 sgid 位。
    - noatime - 不更新文件系统上 inode 访问记录，可以提升性能(参见 atime 参数)。
    - nodiratime - 不更新文件系统上的目录 inode 访问记录，可以提升性能(参见 atime 参数)。
    - relatime - 实时更新 inode access 记录。只有在记录中的访问时间早于当前访问才会被更新。（与 noatime 相似，但不会打断如  mutt 或其它程序探测文件在上次访问后是否被修改的进程。），可以提升性能(参见 atime 参数)。
    - flush - vfat 的选项，更频繁的刷新数据，复制对话框或进度条在全部数据都写入后才消失。
    - defaults - 使用文件系统的默认挂载参数，例如 ext4 的默认参数为:rw, suid, dev, exec, auto, nouser, async.
- `<dump>` dump 工具通过它决定何时作备份. dump 会检查其内容，并用数字来决定是否对这个文件系统进行备份。 允许的数字是 0 和 1 。0 表示忽略， 1 则进行备份。大部分的用户是没有安装 dump 的 ，对他们而言 `<dump>` 应设为 0。
- `<pass>` fsck 读取 `<pass>` 的数值来决定需要检查的文件系统的检查顺序。允许的数字是0, 1, 和2。 根目录应当获得最高的优先权 1, 其它所有需要被检查的设备设置为 2. 0 表示设备不会被 fsck 所检查。


## 设置 Ubuntu 自动挂载硬盘

> 之前都是让Ubuntu自动挂载的，发现有时候重启后，有时自动挂载的目录会发生变化，导致k8s local volume 无法正常读取，也会影响NFS的共享

编辑/etc/fstab文件 （使用`lsblk -f`查看设备UUID）
```
sudo vi /etc/fstab
UUID=0005B7D20000BFA9  /home/user/volume/test ntfs defaults 0 2
# 刷新挂载
sudo mount -a
```

## Ubuntu 安装 NFS

```
sudo apt-get update && sudo apt install -y nfs-kernel-server
```

## 编辑NFS配置文件
```
sudo vi /etc/exports
/home/user/volume/test *(rw,sync,insecure,no_root_squash,no_subtree_check)

# 重载配置
sudo exportfs -a
sudo systemctl restart nfs-kernel-server
```

至此，NFS server 端已经正常工作。

## window 10 自动挂载NFS

win10 本身自带 NFS 服务，但是需要手动开启（中文会有乱码，可以参考后面的方法） [参考](https://hkc.nasclub.vip/wordpress/?p=513)`

## mac 自动挂载

mac 同样 编辑 `/etc/fsta` 写入需要挂载的网络位置即可

```
sudo vi /etc/fsta
192.168.123.***:/home/user/volume/test    /test    nfs    defaults  0 0
sudo mount -a
```

## 电视盒子
- 目前用的是 斐讯N1 刷的盒子 作为电视盒子，用起来还行，软件使用 kodi 即可轻松访问NFS

## 安卓
- 众多软件支持NFS, ES文件浏览器等文件管理软件，或者播放软件 暂时使用 OPlayer 用起来很流畅

## ios 系统
- nplayer

至此，便可以等关注的美剧更新了，回家随便用哪个设备都可以愉快的看剧了。

