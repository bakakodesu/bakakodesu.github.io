---
title: iCloud 同步文件卡住的解决办法
date: 2023-09-13 15:26:06
categories:
- 随笔
tags:
- Mac
math: false
---

原文：[一日一技 | Mac 上 iCloud 云盘同步卡住了？可以试试这样做](https://sspai.com/post/72882)

### 原因

导致 iCloud 云盘同步卡住的原因一般有

- 网络问题
- **一次性向 iCloud 云盘塞入了过多需要同步的文件**
- **文件夹嵌套层级太多**
- Apple ID 转区

我遇到两次同步卡住都是因为2、3点。

如果你**一次性向 iCloud 云盘塞入了过多需要同步的文件**，iCloud 云盘进程在同步前就需要进行大量的计算和检索，花费时间会变长、占用资源变多，如果这时其中有一个文件又问题，很有可能会卡住整个 iCloud 云盘同步的进度。如果桌面或者文稿文件夹中有大量文件的 Mac ，突然打开了对应 iCloud 云盘的「桌面与文稿」同步功能，就非常有可能会出现这样前面提到的情况。

此外，**文件夹嵌套层级太多**也可能是另一个原因，和**一次性向 iCloud 云盘塞入了过多需要同步的文件**的道理相似，过多的文件夹嵌套层级也可能会增加 iCloud 云盘在同步时的计算量与出错风险。而在最新的 [信息](https://sspai.com/link?target=https%3A%2F%2Fwww.v2ex.com%2Ft%2F841605) 中显示，iCloud 云盘似乎已经拒绝同步带有太多嵌套层级的文件夹。

### 解决方案

在终端「分别」输入：

```bash
killall bird
killall cloudd
```

通过 `killall bird` 和 `killall cloudd` 两个命令，将 iCloud 云盘最紧密的两个进程 bird 和 cloudd 进程手动杀死。这两个进程被终止后，macOS 会自动重新将这两个进程拉起，这时我们就可以重新观察 iCloud 云盘的同步情况。
