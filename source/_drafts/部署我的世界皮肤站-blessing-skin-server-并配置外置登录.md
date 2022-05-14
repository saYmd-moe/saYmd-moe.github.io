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
如果你的系统上没有安装 LAMP，可以参考我的这篇文章进行安装 [Ubuntu 22.04 LTS 安装 LAMP](https://saymd-moe.github.io/2022/05/Ubuntu-20-04-%E5%AE%89%E8%A3%85-LAMP/)

### 需要的 PHP 扩展
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


