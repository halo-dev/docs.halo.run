---
title: 在 Centos 7.x 上安装
description: 带有完整依赖安装的详细指南
published: true
date: 2021-01-10T13:50:29.991Z
tags: 
editor: markdown
dateCreated: 2020-10-09T14:40:44.848Z
---

# 说明
本教程以全新的 `CentOS 7.x` 为例，从零开始详细配置并运行 `Halo`，其他 Linux 发行版大同小异。在开始安装前，请确保您已经阅读了[写在前面](/zh/install/prepare)。

# 安装依赖
本教程中需要 JRE 的支持，这里将介绍如何安装这些必需的依赖环境。

## 更新软件包
在执行命令前，保证升级所有包、软件及系统内核

```bash
yum update -y
```

## 安装 JRE（Java Runtime Environment）

注意：从 1.4.3 开始，JRE 的最低版本为 11。

```bash
yum install java-11-openjdk -y
```

验证是否安装成功:

```bash
java -version
```

正确的输出了 JRE 的版本即可。

# 创建新的系统用户

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

登录到 halo 用户
```bash
su - halo
```

# 安装 Halo

## 创建存放[运行包](/install/prepare#%E8%BF%90%E8%A1%8C%E5%8C%85)的目录

> 这里以 `~/app` 为例

```bash
mkdir ~/app && cd ~/app
```

## 下载运行包

如何寻找最快速的下载地址：[/install/downloads](/install/downloads)

```bash
wget https://dl.halo.run/release/halo-1.4.2.jar -O halo.jar
```

## 创建[工作目录](/install/prepare#%E5%B7%A5%E4%BD%9C%E7%9B%AE%E5%BD%95)

```bash
mkdir ~/.halo && cd ~/.halo
```

## 下载示例配置文件到[工作目录](/install/prepare#%E5%B7%A5%E4%BD%9C%E7%9B%AE%E5%BD%95)

```bash
wget https://dl.halo.run/config/application-template.yaml -O ./application.yaml 
```

## 编辑配置文件，配置数据库或者端口等，如需配置请参考[参考配置](/install/config)

```bash
vim application.yaml
```

## 运行 Halo

```bash
cd ~/app && java -jar halo.jar
```

## 如看到类似以下日志输出，则代表启动成功。

```bash
run.halo.app.listener.StartedListener    : Halo started at         http://127.0.0.1:8090
run.halo.app.listener.StartedListener    : Halo admin started at   http://127.0.0.1:8090/admin
run.halo.app.listener.StartedListener    : Halo has started successfully!
```

### 进阶配置

上面我们已经完成了 Halo 的整个配置和安装过程，接下来我们对其进行更完善的配置，比如：`需要开机自启？`，`更简单的启动方式？`

实现以上功能我们只需要新增一个配置文件即可，也就是使用 `Systemd` 来完成这些工作。

如果当前用户为 halo 用户，则需要退出 halo 用户，进入一个拥有管理员权限的用户下：

```bash
# 查看当前登录用户
whoami

# 退出 halo 登录，进入一个有管理员权限的用户
su xxx 或者直接 exit
```

```bash
# 下载 Halo 官方的 halo.service 模板
sudo curl -o /etc/systemd/system/halo.service --create-dirs https://dl.halo.run/config/halo.service
```

下载完成之后，我们还需要对其进行修改。

```bash
# 修改 halo.service
sudo vim /etc/systemd/system/halo.service
```

打开之后我们可以看到

```conf
[Unit]
Description=Halo Service
Documentation=https://halo.run
After=network-online.target
Wants=network-online.target

[Service]
User=halo
Type=simple
ExecStart=/usr/bin/java -server -Xms256m -Xmx256m -jar YOUR_JAR_PATH
ExecStop=/bin/kill -s QUIT $MAINPID
Restart=always
StandOutput=syslog

StandError=inherit

[Install]
WantedBy=multi-user.target
```

参数：
- User=halo：代表当前用户，如果是 root 用户，则删除此条语句。否则，为其对应的用户名
- -Xms256m：为 JVM 启动时分配的内存，请按照服务器的内存做适当调整，512 M 内存的服务器推荐设置为 128，1G 内存的服务器推荐设置为 256，默认为 256。
- -Xmx256m：为 JVM 运行过程中分配的最大内存，配置同上。
- YOUR_JAR_PATH：Halo 安装包的绝对路径，例如 `/www/wwwroot/halo-latest.jar`。

<article class="message is-warning">
  <div class="message-body">

提示

1. 如果你不是按照上面的方法安装的 JDK，请确保 `/usr/bin/java` 是正确无误的。
2. systemd 中的所有路径均要写为绝对路径，另外，`~` 在 systemd 中也是无法被识别的，所以你不能写成类似 `~/halo-latest.jar` 这种路径。
3. 如何检验是否修改正确：把 ExecStart 中的命令拿出来执行一遍。

  </div>
</article>

```bash
# 修改 service 文件之后需要刷新 Systemd
sudo systemctl daemon-reload

# 使 Halo 开机自启
sudo systemctl enable halo

# 启动 Halo
sudo service halo start

# 重启 Halo
sudo service halo restart

# 停止 Halo
sudo service halo stop

# 查看 Halo 的运行状态
sudo service halo status
```

完成以上操作即可通过 `IP:端口` 访问了。不过在此之前，最好先完成后续操作，我们还需要让域名也可以访问到 Halo，请继续看 [配置域名访问](/archives/install-reverse-proxy.html)。
