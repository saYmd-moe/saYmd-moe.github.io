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

Windows在设置里启用系统代理后，终端并不会自动添加环境变量，导致无法使用代理，每次都需要手动添加环境变量，不够优雅，所以我尝试写了一个脚本自动检测和获取系统代理并写入环境变量。


通过读取Windows注册表我们可以得知当前系统代理是否打开，同时也能获取代理地址和端口

```powershell

>$internet_setting = Get-ItemProperty -Path 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet Settings'
>$internet_setting.ProxyEnable
1 # 0、1分别代表系统代理关闭和打开
>$internet_setting.ProxyServer
127.0.0.1:xxxx # xxxx为代理端口

```

然后我们就可以通过`$env:HTTP_PROXY`和`$env:HTTPS_PROXY`来设置环境变量，这样就可以使用代理了

```powershell

>$env:HTTP_PROXY = "http://$($internet_setting.ProxyServer)"
>$env:HTTPS_PROXY = "http://$($internet_setting.ProxyServer)"

```

将上述内容写入`$PROFILE`文件中后，每次启动Powershell时都会自动检测系统代理并添加环境变量

```powershell
>notepad $PROFILE

# 添加以下内容
$internet_setting = Get-ItemProperty -Path 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet Settings'
if ($internet_setting.ProxyEnable -eq 1) {
    $env:HTTP_PROXY = "http://$($internet_setting.ProxyServer)"
    $env:HTTPS_PROXY = "http://$($internet_setting.ProxyServer)"
    Write-Output "Proxy Enabled"
}
else{
    Remove-Item Env:\HTTP_PROXY
    Remove-Item Env:\HTTPS_PROXY
    Write-Output "Proxy Disabled"
}

Remove-Variabl -Name internet_setting

```

这里还遇到过一个问题，如果你是使用VisualStudioCode编辑`$PROFILE`文件，并且在`Write-Output`或`echo`指令后写入了中文，那运行时可能会报错，这是因为VSC使用的是UTF-8编码，而Powershell在中文状态下默认使用的是GBK编码，所以会出现乱码，解决方法是在VSC中将文件编码改为GBK即可。
