---
title: PyCharm 打开文件在 scanning files to index 卡死，或者关闭文件时等待时间过长
date: 2023-09-11 11:34:29
categories:
- [环境配置, PyCharm]
tags:
- Python
math: false
---

### 缓存原因

通过 File - Invalidate caches... 的方式清理缓存。

### 网盘原因

因为我的工程文件是放在 Dropbox 里的。有时候会因为 Dropbox 在同步文件导致打开工程文件后，在建立和更新索引时卡住。
