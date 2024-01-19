---
title: Mac 串口查询之 /dev/cu. 与 /dev/tty. 记录
date: 2023-07-28 12:56:24
categories:
- 随笔
tags:
- Mac
- 传感器
math: false
---

输入指令`ls /dev/cu.*`，可获得 Mac 的串口信息。内容如下

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/Mac-串口查询之-dev-cu-与-dev-tty-记录1.png)

输入指令`cd /dev`回车，再输入指令`ls *tty`，也可查询串口信息。内容如下

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/Mac-串口查询之-dev-cu-与-dev-tty-记录2.png)

之前 Mac 与 Arduino 通信的时候，串口的格式为`/dev/tty.Arduino`。最近在调试 N100 九轴传感器，发现用「/tty.」格式的话识别不到传感器，即`/dev/tty.N100`不行，必须使用`/dev/cu.N100`。不知道为什么。

另外我在想，Arduino 用`/dev/cu.Arduino`这个格式能行吗...
