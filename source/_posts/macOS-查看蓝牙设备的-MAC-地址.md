---
title: macOS 查看蓝牙设备的 MAC 地址
date: 2023-09-11 17:23:10
categories:
- 随笔
tags:
- Mac
- 传感器
math: false
---

安装所需软件:

```bash
brew install bluetoothconnector
```

目标设备之前必须与 Mac 成功匹配过。

在终端使用下面的命令得到设备的 MAC 地址:

```bash
BluetoothConnector
```

会得到类似这样的输出：

```yaml
70-ae-d5-c8-48-84 - ᖰ´༥`ᖳ
80-82-23-cc-20-2e - AirPods
98-d3-02-96-9a-bb - H-C-2010-06-01
a0-78-17-e3-dd-38 - TrackPaaaaaaad
ac-90-85-7f-18-5b - AirPods Plus++
```

