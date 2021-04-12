---
title: 腾讯云 CloudBase
description: 使用腾讯云 CloudBase 一键部署
published: true
date: 2021-04-12T12:43:31.665Z
tags: 
editor: markdown
dateCreated: 2021-04-12T10:55:32.568Z
---

# 声明

1. 本组织与腾讯云官方无任何合作和利益关系。
2. 您在使用期间如果有腾讯云所带来的问题，均与我们无关。
3. 开始之前，我们默认认为您已经了解过[腾讯云云开发](https://cloud.tencent.com/product/tcb)。

# 注意事项

1. 系统使用内置的 H2 Database，暂不支持使用 MySQL。
1. 工作目录保存在腾讯云提供的 CFS 上，在使用此方式创建应用的时候会要求创建 CFS。
1. 目前使用该方式部署，不支持修改[配置文件](https://docs.halo.run/zh/install/config)。
1. 费用相关请参考 https://cloud.tencent.com/document/product/876/18864

# 进入云开发页面

点击下方按钮即可进入到腾讯云云开发 CloudBase 创建应用界面，此按钮可在云开发页面自动选择 Halo 的配置模板。

[![](https://main.qcloudimg.com/raw/67f5a389f1ac6f3b4d04c7256438e44f.svg)](https://console.cloud.tencent.com/tcb/env/index?action=CreateAndDeployCloudBaseProject&appUrl=https%3A%2F%2Fgithub.com%2Fhalo-dev%2Ftencent-cloudbase-halo&branch=master)

# 配置

填写环境名称

应用配置

提交授权

# 相关链接

- [Halo 配置文件仓库](https://github.com/halo-dev/tencent-cloudbase-halo)
- [腾讯云 CloudBase](https://console.cloud.tencent.com/tcb/env/index)
- [云开发使用指南](https://cloud.tencent.com/document/product/876)

