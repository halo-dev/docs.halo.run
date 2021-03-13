---
title: 在 Windows 服务器上部署
description: 
published: true
date: 2021-03-13T06:43:47.159Z
tags: 
editor: markdown
dateCreated: 2020-10-09T12:52:31.360Z
---

> 在继续操作之前，我们推荐您先阅读[《写在前面》](/install/prepare)，这可以快速帮助你了解 Halo。
{.is-info}
# 系统要求
目前运行 Halo 的最低依赖要求为 JRE 11，而 Java9 之后将不再提供 32 位系统的环境，因此请确保您的服务器属于 64 位 CPU。

# 依赖检查
如下将介绍在 Windows 下安装 OpenJRE 11 的方式。如果您的服务器已经安装过 OpenJRE 11，则可以直接跳过本节。

1. 前往 https://developers.redhat.com/content-gateway/file/java-11-openjdk-jre-11.0.10.9-1.windows.redhat.x86_64.msi 下载 OpenJRE 11 的可执行程序。

2. 下载时会提示登录“红帽”，任意注册账号登录即可。登录完成之后会自动下载 JRE。

3. 双击 MSI 安装程序，安装 JRE 至服务器，注意到安装程序第三步时，勾选 `JAVA_HOME Variable`。
