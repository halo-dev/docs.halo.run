---
title: 使用 OneinStack 管理 Nginx 反向代理
description: 使用 OneinStack 的 vhost 脚本创建 Halo 站点的 Nginx 配置文件
published: true
date: 2021-05-16T07:47:25.367Z
tags: 
editor: markdown
dateCreated: 2021-05-16T07:06:37.017Z
---

# Halo 部署

参见 [在 Linux 环境部署](/install/linux)

> `「反向代理」` 部分可以不进行操作，保证 Halo 服务运行无误即可。
{.is-info}

# 通过 OneinStack 安装 Nginx

点击下方链接进入 OneinStack 官网，仅选择 `安装 Nginx`，其他的都可以取消选择。

https://oneinstack.com/auto

最后点击 `复制安装命令` 到服务器执行即可。如果你仅安装 Nginx，你的链接应该是这样：

```bash
wget -c http://mirrors.linuxeye.com/oneinstack-full.tar.gz && tar xzf oneinstack-full.tar.gz && ./oneinstack/install.sh --nginx_option 1
```

> 这一步会经过编译安装，可能会导致安装时间很漫长，这主要取决于你服务器的性能。
{.is-info}

出现下面的信息即代表安装成功：

```bash
Nginx installed successfully!
Created symlink from /etc/systemd/system/multi-user.target.wants/nginx.service to /usr/lib/systemd/system/nginx.service.
Redirecting to /bin/systemctl start nginx.service
####################Congratulations########################
Total OneinStack Install Time: 5 minutes

Nginx install dir:              /usr/local/nginx
```

# 创建 vhost

> 即创建一个站点，你可以通过这样的方式在你的服务器创建无限个站点。接下来的目的就是创建一个站点，并反向代理到 Halo。这一步在此教程使用 `demo.halo.run` 这个域名做演示，实际情况请修改此域名。

1. 进入到 oneinstack 目录，执行 vhost 创建命令

```bash
cd oneinstack
```

```bash
sh vhost.sh
```

2. 按照提示选择或输入相关信息

```bash	
What Are You Doing?
	1. Use HTTP Only
	2. Use your own SSL Certificate and Key
	3. Use Let's Encrypt to Create SSL Certificate and Key
	q. Exit
Please input the correct option:
```

这一步是选择证书配置方式，如果你有自己的证书，输入 <kbd>2</kbd> 即可。如果需要使用 `Let's Encrypt` 申请证书，选择 <kbd>3</kbd> 即可。

```bash
Please input domain(example: www.example.com):
```

输入自己的域名即可，前提是已经提前解析好了域名。

```bash
Please input the directory for the domain:demo.halo.run :
(Default directory: /data/wwwroot/demo.halo.run):
```

提示输入站点根目录，因为我们是使用 Nginx 的反向代理，所以这个目录是没有必要配置的，我们直接使用默认的即可（直接回车）。

```bash
Do you want to add more domain name? [y/n]:
```

是否需要添加其他域名，按照需要选择即可，如果不需要，输入 <kbd>n</kbd> 并回车确认。

```bash
Do you want to add hotlink protection? [y/n]:
```

是否需要做防盗链处理，按照需要选择即可。

```bash
Allow Rewrite rule? [y/n]:
```

路径重写配置，我们不需要，选择 <kbd>n</kbd> 回车确定即可。

```bash
Allow Nginx/Tengine/OpenResty access_log? [y/n]:
```

Nginx 的请求日志，建议选择 <kbd>y</kbd>。

这样就完成了 vhost 站点的创建，最终会输出站点的相关信息：

```bash
Your domain:                  demo.halo.run
Virtualhost conf:             /usr/local/nginx/conf/vhost/demo.halo.run.conf
Directory of:                 /data/wwwroot/demo.halo.run
```

Nginx 的配置文件即 `/usr/local/nginx/conf/vhost/demo.halo.run.conf`。

# 修改 Nginx 配置文件

上方创建 vhost 的过程并没有创建反向代理的配置，所以需要我们自己修改一下配置文件。

1. 使用你熟悉的工具打开配置文件，此教程使用 vim。

```bash
vim /usr/local/nginx/conf/vhost/demo.halo.run.conf
```

2. 删除一些不必要的配置

```nginx
  location ~ [^/]\.php(/|$) {
    #fastcgi_pass remote_php_ip:9000;
    fastcgi_pass unix:/dev/shm/php-cgi.sock;
    fastcgi_index index.php;
    include fastcgi.conf;
  }
```

此段配置是针对 php 应用的，所以可以删掉。

3. 添加 `upstream` 配置

在 `server` 的同级节点添加如下配置：

```nginx
upstream halo {
  server 127.0.0.1:8090;
}
```

4. 在 `server` 节点添加如下配置

```nginx
  location / {
    proxy_set_header HOST $host;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_pass http://halo;
  }
```

5. 修改 `location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|flv|mp4|ico)$` 节点

```nginx
  location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|flv|mp4|ico)$ {
    proxy_pass http://halo;
    expires 30d;
    access_log off;
  }
```

6. 修改 `location ~ .*\.(js|css)?$` 节点

```nginx
  location ~ .*\.(js|css)?$ {
    proxy_pass http://halo;
    expires 7d;
    access_log off;
  }
```
