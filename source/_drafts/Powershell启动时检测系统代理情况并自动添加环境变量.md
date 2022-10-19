---
title: Powershell启动时检测系统代理情况并自动添加环境变量
tags:
    - pwsh
    - script
categories:
    - [script, pwsh]
---

在本文中，你将看到：  
如何让Powershell启动时自动检测系统代理是否打开并自动添加环境变量
<!-- more -->

Windows在设置里启用系统代理后，终端并不会自动添加环境变量，导致无法使用代理，每次都需要手动添加环境变量，不够优雅（

## 1. 检测系统代理

通过读取Windows注册表我们可以得知当前系统代理是否打开，同时也能获取代理地址和端口

```powershell

$proxy = Get-ItemProperty -Path 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet Settings' -Name ProxyEnable, ProxyServer


```


