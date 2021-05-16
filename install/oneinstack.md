---
title: 使用 OneinStack 管理 Nginx 反向代理
description: 使用 OneinStack 的 vhost 脚本创建 Halo 站点的 Nginx 配置文件
published: true
date: 2021-05-16T07:17:04.608Z
tags: 
editor: markdown
dateCreated: 2021-05-16T07:06:37.017Z
---

# Halo 部署

参见 [在 Linux 环境部署](/install/linux)

> `「反向代理」` 部分可以不进行操作，保证 Halo 服务运行无误即可。
{.is-info}

# 通过 OneinStack 安装 Nginx

点击下方链接进入 OneinStack 官网，仅选择 `安装Nginx`，其他的都可以取消选择。

https://oneinstack.com/auto

最后点击 `复制安装命令` 到服务器执行即可。如果你仅安装 Nginx，你的链接应该是这样：

```bash
wget -c http://mirrors.linuxeye.com/oneinstack-full.tar.gz && tar xzf oneinstack-full.tar.gz && ./oneinstack/install.sh --nginx_option 1
```

> 这一步会经过编译安装，可能会导致安装时间很长。
{.is-info}