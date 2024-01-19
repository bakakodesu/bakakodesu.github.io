---
title: Xcode 真机调试 iWatch 的坑
date: 2024-01-19 15:46:38
categories:
- 随笔
tags:
- iOS Development
math: false
---

总之而言，不要在 Xcode 上直接选择真机来运行 Watch App 工程。

由于 Watch App 不能从电脑直接安装，Xcode 需要首先把 swift 的运行时和一系列安装脚本先传到手机上，再由手机完成安装过程。之后 Xcode 还需要通过手机来和 Watch 建立连接才能调试。

这样的结果就是 Xcode 不停地提示正在连接手表以及正在重新连接手表。

最好的办法是，选择 iOS App，直接在 iPhone 上运行。

因为 iOS App 在安装的时候会把 Watch App 同样编译安装，之后只要手动在「手表」应用中重新安装 Watch App 即可。
