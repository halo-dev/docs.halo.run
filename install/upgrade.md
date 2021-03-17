---
title: 版本升级
description: 版本升级指南
published: true
date: 2021-03-17T13:22:13.161Z
tags: 
editor: markdown
dateCreated: 2020-10-09T12:53:58.281Z
---

选择你部署的平台：

# Tabs {.tabset}
## Linux <i class="mdi mdi-ubuntu"></i>

> 我们假设您存放运行包的路径为 `~/app`，运行包的文件名为 `halo.jar`，如有不同，下列命令请按需修改。

---

> 从 1.4.3 开始，Halo 最低支持的 JRE 版本为 11，在升级前，请务必先升级 JRE。
{.is-warning}

> 如果当前您不方便升级到 11，我们推荐使用 [Docker](/install/docker) 运行新版 Halo，从 Jar 包的方式迁移到 Docker 运行非常方便，按照[指南](/install/docker)在创建容器的时候将容器内的 `/root/.halo` 目录映射到当前 Halo 的工作目录即可。
{.is-info}

**JRE 升级方案**（针对按照旧版本文档安装 JRE 的用户）：

如果您之前是按照文档安装的 JRE，可以尝试使用下面的命令来升级。

查看当前安装的版本：

```bash
rpm -qa|grep jdk
```

卸载 `jdk1.8.0` 开头的那个包（包名可能会不一样）：

```bash
yum remove java-1.8.0-openjdk-1.8.0_121-fcs.x86_64
```

```bash
yum remove java-1.8.0-openjdk-headless-1.8.0.275.b01-1.el8_3.x86_64
```

安装 OpenJRE 11：

```bash
yum install java-11-openjdk -y
```

---

1. 停止正在运行的服务

```bash
service halo stop
```

2. 备份数据以及旧的运行包（重要）

```bash
cp -r ~/.halo ~/.halo.1.4.6
```

```bash
cd ~/app && mv halo.jar halo.jar.1.4.6
```

3. 下载最新版本的运行包

```bash
cd ~/app && wget https://dl.halo.run/release/halo-1.4.7.jar -O halo.jar
```

> 如果下载速度不理想，可以[在这里](/install/downloads)选择其他下载地址。
{.is-info}


4. 启动测试

```bash
java -jar halo.jar
```

> 如测试启动正常，请继续下一步。使用 <kbd>CTRL</kbd>+<kbd>C</kbd> 停止运行测试进程。
{.is-info}

5. 重启服务

```
service halo start
```


## Docker <i class="mdi mdi-docker"></i>

1. 停止并删除当前运行中的容器

```bash
docker stop halo
```

```bash
docker rm -f halo
```

> 你的容器名称不一定为 `halo`，在执行前可以先执行 `docker ps -a` 查看一下。
{.is-info}

2. 拉取最新的 Halo 镜像

```bash
docker pull halohub/halo
```

> 查看最新版本镜像：[https://hub.docker.com/r/halohub/halo](https://hub.docker.com/r/halohub/halo)
{.is-info}

> 从 1.4.3 开始，Docker 镜像已经转移到 `halohub` 组织，不再是 `ruibaby/halo`
{.is-info}

3. 创建容器

```bash
docker run -it -d --name halo -p 8090:8090 -v ~/.halo:/root/.halo --restart=always halohub/halo
```
- **-it：** 开启输入功能并连接伪终端
- **-d：** 后台运行容器
- **--name：** 为容器指定一个名称
- **-p：** 端口映射，格式为 `主机(宿主)端口:容器端口` ，可在 `application.yaml` 配置。
- **-v：** 工作目录映射。形式为：-v 宿主机路径:/root/.halo，后者不能修改。
- **--restart：** 建议设置为 `always`，在 Docker 启动的时候自动启动 Halo 容器。