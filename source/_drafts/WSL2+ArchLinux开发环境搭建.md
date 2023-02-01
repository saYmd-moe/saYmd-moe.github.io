---
title: WSL2+ArchLinux开发环境搭建
tags:
  - Linux
  - ArchLinux
  - Environment
categories:
  - [IT, Linux, ArchLinux]
  - [IT, Linux, WSL]
---


作为一个理科狗，虽然很享受Linux系统精简的操作逻辑和完美的终端体验，但不少学业上的需求都要由Windows系统来完成，显然双系统非常不方便——我不能随时在两个系统中切换. 于是我又重新用起了WSL，但是我想要使用的ArchLinux并没有官方支持，好在可以通过导出的系统镜像创建distrod，具体方法来自b站用户 [@SpiritblueM](https://space.bilibili.com/86338199) [从零开始，WSL2+ArchLinux极简系统安装](https://www.bilibili.com/read/cv17164011)  . 添加部分个人的配置，主要用作个人学习备份，在此表示感谢. 
<!-- more -->


# Windows配置

首先需要打开wsl支持，在可选功能中勾选 `Hyper-V` 和 `适用于Linux的Windows子系统` 后重启即可.

1.下载官方的 `Ubuntu` 系统

打开 `powershell` 输入以下命令创建一个`ubuntu` 分发版. 

```powershell
wsl --update
wsl --install ubuntu
```

按提示创建用户名和密码，然后就可以进入ubuntu系统了.


2.安装docker desktop
```powershell
winget install -e --id Docker.DockerDesktop
```


# 导出ArchLinux镜像

在Ubuntu端输入以下命令

```bash
docker pull archlinux
docker run archlinux
id=$(docker ps -a | grep archlinux | awk '{print $1}')
docker export $id > /mnt/d/wsl/sys_imag/arch.tar
```

这一步会将ArchLinux的镜像导出到 `D:\wsl\sys_imag\arch.tar` .

随后就可以把Ubuntu删除了，在`powershell`中输入以下命令. 

```powershell
wsl --unregister ubuntu
```


# 