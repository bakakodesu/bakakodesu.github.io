---
title: '树莓派蓝牙连接 HC05 提示 Failed to connect: org.bluez.Error.NotAvailable 解决办法'
date: 2023-09-11 17:38:37
categories:
- 随笔
tags:
- Raspberry Pi
math: false
---

树莓派在连接 HC05 蓝牙模块时遇见了一些问题。

虽然成功配对：

```yaml
[bluetooth]# pair 98:D3:02:96:9A:BB
Attempting to pair with 98:D3:02:96:9A:BB
[CHG] Device 98:D3:02:96:9A:BB Connected: yes
Request PIN code
[agent] Enter PIN code: [DEL] Device 4D:9E:4F:54:59:22 4D-9E-4F-54-59-22
[agent] Enter PIN code: [DEL] Device 10:05:6C:76:F6:F0 MC76F6F0
[agent] Enter PIN code: 1234
[CHG] Device 98:D3:02:96:9A:BB UUIDs: 00001101-0000-1000-8000-00805f9b34fb
[CHG] Device 98:D3:02:96:9A:BB ServicesResolved: yes
[CHG] Device 98:D3:02:96:9A:BB Paired: yes
Pairing successful
[CHG] Device 98:D3:02:96:9A:BB ServicesResolved: no
[CHG] Device 98:D3:02:96:9A:BB Connected: no
```

但可以看到自动断开了。

再次连接时，提示 `Failed to connect: org.bluez.Error.NotAvailable`，如下面所示：

```yaml
[bluetooth]# connect 98:D3:02:96:9A:BB
Attempting to connect to 98:D3:02:96:9A:BB
[CHG] Device 98:D3:02:96:9A:BB Connected: yes
[CHG] Device 98:D3:02:96:9A:BB ServicesResolved: yes
Failed to connect: org.bluez.Error.NotAvailable
[CHG] Device 98:D3:02:96:9A:BB ServicesResolved: no
[CHG] Device 98:D3:02:96:9A:BB Connected: no
```

在 GUI 上进行连接，则会提示 `Connection failed - No usable services on this device`。

此处问题并非个例、在其它帖子里看到过相同问题。

真正的原因是默认情况下树莓派蓝牙始终是作为从模式，所以主动连接的话，无论什么设备都会秒掉线（存疑）。

下面是解决办法：

不再通过 bluetoothctl 工具进行连接，而是：

```bash
sudo rfcomm connect hci0 98:D3:02:96:9A:BB
```

参考：

1. [Failed to connect: org.bluez.Error.NotAvailable](https://forums.raspberrypi.com/viewtopic.php?t=162911&sid=86d298a19589e7836b4eb2408a920bde)；
2. [Raspberry Pi 3 Unable to Connect to Arduino with HC-06](https://forums.raspberrypi.com/viewtopic.php?t=163712&sid=3d7b1a34f4fcf6cfcafecdb3d345bf15)
3. [Failed to connect: org.bluez.Error.NotAvailable - Error](https://raspberrypi.stackexchange.com/questions/82173/failed-to-connect-org-bluez-error-notavailable-error)；
4. [树莓派和HC-05蓝牙连接以及通过串口写入（Write）和读入(Read)数据](https://blog.csdn.net/jinanhezhuang/article/details/109250208)。
