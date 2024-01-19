---
title: M1 Mac Parallels Desktop Windows11 安装 CP210x 串口驱动
date: 2023-07-27 17:38:43
categories:
- 随笔
tags:
- Mac
- 传感器
math: false
---

> 设备作为虚拟 COM 端口工作时，需要 CP210x USB 至 UART 桥虚拟 COM 端口 (VCP) 驱动器以促进与 CP210x 产品的主机通信。也可使用直接存取驱动器将这些设备连接至主机。

我需要在 Windows 系统下调试 WHEELTEC 的 9 轴传感器 N100。由于办公电脑是 M1 的 Mac Air，所以只能在 Parallels Desktop 上进行（虚拟机系统为 Windows11）。

插上 USB 后系统可以识别到串口设备，但是在设备管理器中错误代码 28，说明无驱动。之前问店家要了一份相关资料，内有 CP2102 的驱动安装包。安装了，但没用。大概率是 ARM 架构的问题。

于是跑 silicon lab 官网找最新驱动，这是驱动下载[地址](https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers?tab=downloads)。

下载第一个「CP210x Universal Windwos Driver」即可。

![网页是这样的](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/M1-Mac-Parallels-Desktop-Windows11-安装-CP210x-串口驱动1.png)

解压出来的文件内容是这样的：

![可以注意到arm和arm64](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/M1-Mac-Parallels-Desktop-Windows11-安装-CP210x-串口驱动2.png)

接下来只需要右键 silabser.inf 安装即可。

![需要点击「显示更多选项」](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/M1-Mac-Parallels-Desktop-Windows11-安装-CP210x-串口驱动3.png)

打开设备管理器，代码28搞定，驱动打上了。

![开心的调试吧!](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/M1-Mac-Parallels-Desktop-Windows11-安装-CP210x-串口驱动4.png)

如果要在 Mac 系统下与 N100 通信，下载「CP210x VCP Mac OSX Driver」，内有一个DMG格式的文件，打开安装即可。树莓派（Linux）则不需要安装驱动就能与 N100 进行通信。
