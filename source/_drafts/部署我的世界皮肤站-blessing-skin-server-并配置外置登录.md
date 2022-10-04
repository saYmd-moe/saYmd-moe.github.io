---
title: 部署我的世界皮肤站 blessing skin server 并配置外置登录
tags:
    - MC
categories:
    - [game, MC]
---

在本文中，你将看到：  
如何使用 docker 和手动部署我的世界皮肤站 blessing skin server v6  
如果本文中有内容出现差错，请以[官方WIKI](https://blessing.netlify.app/)为准
<!-- more -->

# 一、使用 docker 快速部署


# 二、手动部署

## 1、安装前准备

请确保你的系统已经安装并配置完成 LAMP 或 LNMP  
这里以 LAMP 作为示范，它更适合新手快速上手  
如果你的系统上没有安装 LAMP，可以参考这篇文章进行安装 [Ubuntu 22.04 LTS 安装 LAMP](https://saymd-moe.github.io/2022/05/Ubuntu-20-04-%E5%AE%89%E8%A3%85-LAMP/)

### (1) 需要的 PHP 扩展
请确保已安装并启用一下扩展
- OpenSSL
- PDO
- Mbstring
- Tokenizer
- GD
- XML
- Ctype
- JSON
- fileinfo
- zip  

使用这个命令安装缺少的扩展
```bash
# xxx 为扩展名称，全小写
sudo apt-get install php-xxx
```

### (2) 准备安装文件

从该项目的 release 中下载最新版安装包：[GitHub Releases](https://github.com/bs-community/blessing-skin-server/releases)

将压缩包解压到任意位置，我这里解压到目录 ```~/bs``` 下。  
需要保证 ```index.php``` 文件在网站根目录下的 ```public``` 目录下。  

将 ```.env.example``` 文件重命名为 ```.env```

因为 Blessing Skin 需要 ```APP_KEY``` 来加密 Session 等数据，所以需要使用下列命令生成 app key：
```bash
php artisan key:generate
```

## 2、配置 URL 重写规则（伪静态）

这一步是最关键也是坑最多的一步（

### (1) 启用 Apache2 Rewrite 支持

执行加载 Rewrite 模块并重启 apache2
```bash
a2enmod rewrite
sudo systemctl restart apache2
```

### (2) 修改 URL 重写

使用任意你喜欢的编辑器修改配置

```bash
sudo nano /etc/apache2/sites-available/000-default.conf
```

这里假设你和我一样，将 blessing-skin-server 的压缩包解压到了 ```~/bs``` 位置，假设用户名为 ```username```
```conf
# 在 <VirtualHost *:80>...</VirtualHost> 中添加以下内容
<Directory /home/username/bs/public/>
    Options Indexes FollowSymLimks
    AllowOverride All
    Require all granted
    DirectoryIndex index.php
</Directory>
```

然后重启 apache2
```bash
sudo systemctl restart apache2
```

## 