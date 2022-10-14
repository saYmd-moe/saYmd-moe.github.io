---
title: archlinux和manjaro从AUR安装软件时bsdtar报错问题：已解决
tags:
  - Linux
  - Manjaro
  - AUR
categories:
  - - IT
    - Linux
    - Manjaro
    - AUR
index_img: /img/in_post/banner0.png
banner_img: /img/in_post/banner0.png
date: 2022-10-15 01:05:48
---



archlinux和manjaro从AUR安装软件时bsdtar报错问题
<!-- more -->

这两天将主力机换上了Manjaro系统，本来打算上archlinux，但是被显卡驱动搞麻了（fxxk U，NVIDIA），在惊叹于AUR丰富软件源的同时也遇到了这个报错，在国内网站查询并未找到解决方案，巧合之下在archlinux论坛上找到了相关的帖子，我遇到该问题后进行的尝试和原帖楼主类似，原文链接：[Index/AUR Issues, Discussion & PKGBUILD Requests/Cannot install anything AUR with bsdtar broken](https://bbs.archlinux.org/viewtopic.php?pid=2061695)，有一定英语能力的可以参阅原帖。

# 一、报错内容

在使用`yay -S rulu-jre-fx-bin`命令安装`rulu-jre-fx-bin`时，`bsdtar`报错如下： 
```source
bsdtar: Option --no-read-sparse is not supported
Usage:
  List:    bsdtar -tf <archive-filename>
  Extract: bsdtar -xf <archive-filename>
  Create:  bsdtar -cf <archive-filename> [filenames...]
  Help:    bsdtar --help
```
导致软件构建失败

# 二、猜测原因

既然是`bsdtar`报错，我自然就想到会不会是Manjaro使用的软件版本问题，于是我尝试对`tar`进行降级，但仍然得到相同的报错。


# 三、解决方案

解决方案由[archlinux Form](https://bbs.archlinux.org/)用户seth给出

依次执行以下命令
```source
type tar
type bsdtar
pacman -Qi tar libarchive
pacman -Qo bsdtar
man bsdtar | grep sparse
```

分别得到以下信息
```source
>type tar
tar is /usr/bin/tar
>type bsdtar
bsdtar is /home/saymd/anaconda3/bin/bsdtar
>pacman -Qi tar libarchive
名字           : tar
版本           : 1.34-1
描述           : Utility used to store, backup, and transport files
架构           : x86_64
URL            : https://www.gnu.org/software/tar/
软件许可       : GPL3
组             : 无提供           : 无依赖于         : glibc  acl  attr
可选依赖       : 无依赖它         : base  lib32-libltdl  libtool
被可选依赖     : 无与它冲突       : 无取代           : 无安装后大小     : 2.88 MiB
打包者         : Sébastien Luttringer <seblu@seblu.net>
编译日期       : 2021年02月13日 星期六 21时17分10秒安装日期       : 2022年08月16日 星期二 20时12分44秒安装原因       : 单独指定安装安装脚本       : 否验证者         : 数字签名
名字           : libarchive
版本           : 3.6.1-2
描述           : Multi-format archive and compression library
架构           : x86_64
URL            : https://libarchive.org/
软件许可       : BSD
组             : 无提供           : libarchive.so=13-64
依赖于         : acl  libacl.so=1-64  bzip2  expat  lz4  openssl  xz  zlib  zstd
可选依赖       : 无依赖它         : ark  cmake  flatpak  fwupd  mkinitcpio  mpv  ostree  pacman  pkgfile  smbclient  vlc
被可选依赖     : 无与它冲突       : 无取代           : 无安装后大小     : 1129.74 KiB
打包者         : Christian Hesse <eworm@archlinux.org>
编译日期       : 2022年08月02日 星期二 05时34分30秒安装日期       : 2022年08月16日 星期二 20时12分42秒安装原因       : 作为其他软件包的依赖关系安装安装脚本       : 否验证者         : 数字签名
>pacman -Qo bsdtar
错误：没有软件包拥有 /home/saymd/anaconda3/bin/bsdtar
```

到这里就恍然大悟，从第4行和最后一行可以看出，终端运行在conda的虚拟环境中，统的`bsdtar`被屏蔽了，只需要完全退出虚拟环境再进行软件的安装即可成功。

```source
# 请确保完全退出虚拟环境
conda deactivate
```

# 四、总结

在这个帖子中，版主V1del说了这样一段话：

>Don't make conda the base environment of your entire shell that's guaranteed to run you into weird issues with more than just this case here. Run conda explicitly in a terminal you dedicate to "conda" stuff, when you are actually doing conda stuff.
