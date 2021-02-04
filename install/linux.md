---
title: 在 Linux 环境部署
description: 在Linux上快速安装Halo
published: true
date: 2021-02-04T13:45:38.766Z
tags: 
editor: markdown
dateCreated: 2020-10-09T12:51:55.006Z
---

> 在继续操作之前，我们推荐您先阅读[《写在前面》](/install/prepare)，这可以快速帮助你了解 Halo。
{.is-info}

# 依赖检查
目前运行 Halo 的最低依赖要求为 JRE 11，请务必确保在进行下面操作之前已经正确安装了 JRE。

检查 JRE 版本：

```bash
java -version
```

如果正确输出了 JRE 的版本，那么请继续进行下面的操作。此文档不会包含 JRE 的具体安装方式。

# 安装

1. 创建新的系统用户
> 我们不推荐直接使用系统 root 用户来运行 Halo。如果您需要直接使用 root 用户，请跳过这一步。
{.is-info}

创建一个名为 halo 的用户（名字可以随意）
```bash
useradd -m halo
```

给予 sudo 权限
```bash
usermod -aG wheel halo
```

为 halo 用户创建密码
```bash
passwd halo
```

登录到 halo 账户
```bash
su - halo
```

2. 创建存放[运行包](/install/prepare#%E8%BF%90%E8%A1%8C%E5%8C%85)的目录
> 这里以 `~/app` 为例

```
mkdir ~/app && cd ~/app
```

3. 下载运行包
```bash
wget https://dl.halo.run/release/halo-1.4.3.jar -O halo.jar
```

> 如果下载速度不理想，可以[在这里](/install/downloads)选择其他下载地址。
{.is-info}

4. 创建[工作目录](/install/prepare#%E5%B7%A5%E4%BD%9C%E7%9B%AE%E5%BD%95)

```bash
mkdir ~/.halo && cd ~/.halo
```

5. 下载示例配置文件到[工作目录](/install/prepare#%E5%B7%A5%E4%BD%9C%E7%9B%AE%E5%BD%95)
```bash
wget https://dl.halo.run/config/application-template.yaml -O ./application.yaml 
```

6. 编辑配置文件，配置数据库或者端口等，如需配置请参考[参考配置](/install/config)
```bash
vim application.yaml
```

7. 测试运行 Halo
```bash
cd ~/app && java -jar halo.jar
```

8. 如看到类似以下日志输出，则代表启动成功。
```bash
run.halo.app.listener.StartedListener    : Halo started at         http://127.0.0.1:8090
run.halo.app.listener.StartedListener    : Halo admin started at   http://127.0.0.1:8090/admin
run.halo.app.listener.StartedListener    : Halo has started successfully!
```
打开 `http://ip:端口号` 即可看到安装引导界面。

> 如测试启动正常，请继续看`作为服务运行`部分，第 8 步仅仅作为测试。当你关闭 ssh 连接之后，服务会停止。你可使用 <kbd>CTRL</kbd>+<kbd>C</kbd> 停止运行测试进程。
{.is-info}

> 如果需要配置域名访问，建议先配置好反向代理以及域名解析再进行初始化。如果通过 `http://ip:端口号` 的形式无法访问，请到服务器厂商后台将运行的端口号添加到安全组，如果服务器使用了 Linux 面板，请检查此 Linux 面板是否有还有安全组配置，需要同样将端口号添加到安全组。
{.is-warning}

# 作为服务运行

1. 退出 halo 账户，登录到 root 账户

> 如果当前就是 root 账户，请略过此步骤。

```bash
exit
```

2. 下载 Halo 官方的 halo.service 模板
```bash
wget https://dl.halo.run/config/halo.service -O /etc/systemd/system/halo.service
```

3. 修改 halo.service
```bash
vim /etc/systemd/system/halo.service
```

4. 修改配置
- **YOUR_JAR_PATH**：Halo 安装包的绝对路径，例如 `/home/halo/app/halo.jar` 。
- **USER**：运行 Halo 的系统用户，如果有按照上方教程创建新的用户来运行 Halo，修改为你创建的用户名称即可。反之请删除 `User=USER`。

```ini
[Unit]
Description=Halo Service
Documentation=https://halo.run
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
User=USER
ExecStart=/usr/bin/java -server -Xms256m -Xmx256m -jar YOUR_JAR_PATH
ExecStop=/bin/kill -s QUIT $MAINPID
Restart=always
StandOutput=syslog

StandError=inherit

[Install]
WantedBy=multi-user.target
```
>  请确保 `/usr/bin/java` 是正确无误的。建议将 `ExecStart` 中的命令复制出来运行一下，保证命令有效。
{.is-warning}

5. 重新加载 systemd
```bash
systemctl daemon-reload
```

6. 运行服务
```bash
systemctl start halo
```

7. 在系统启动时启动服务
```bash
systemctl enable halo
```

您可以查看服务日志检查启动状态
```bash
journalctl -n 20 -u halo
```

# 反向代理

你可以在下面的反向代理软件中任选一项，我们假设你已经安装好了其中一项，并对其基本操作有一定了解。

## Nginx

```nginx
upstream halo {
  server 127.0.0.1:8090;
}
server {
  listen 80;
  listen [::]:80;
  server_name youdomain.com;
  client_max_body_size 1024m;
  location / {
    proxy_pass http://halo;
    proxy_set_header HOST $host;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}
```

## Caddy 1.x

```
https://www.youdomain.com {
 gzip
 tls your@email.com
 proxy / localhost:8090 {
  transparent
 }
}
```

## Caddy 2.x

```
www.youdomain.com

encode gzip

reverse_proxy 127.0.0.1:8090
```

以上配置都可以在 https://github.com/halo-dev/halo-common 找到。
