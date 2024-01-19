---
title: '使用 Cocoapods 时报错 Sandbox: rsync.samba(62830) deny(1)'
date: 2024-01-10 23:31:33
categories:
- [环境配置, Xcode]
tags:
- iOS Development
math: false
---

### 问题描述

在 Xcode 新建项目并 pod install 后，run 的时候，遇到了如下的问题：

沙盒报错

```sh
Sandbox: rsync.samba(62830) deny(1)
```

### 解决办法

在 TARGETS 的 Build Settings 中选择「All」与「Combined」之后搜索：

```sh
User Script Sandboxing
```

将其改为 NO 即可解决。

参考 [iOS Xcode 15 Sandbox: rsync(xxxx) deny(1) file-write-create](https://blog.csdn.net/lyz0925/article/details/133944802)
