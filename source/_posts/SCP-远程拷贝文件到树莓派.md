---
title: SCP 远程拷贝文件到树莓派
date: 2023-08-11 22:44:29
categories:
- 随笔
tags:
- Raspberry Pi
math: false
---

```bash
scp file user@host:target path

eg. 
scp main.py pi@192.168.1.1:/home/pi/Desktop/demo
将目录下的 main.py 拷贝至树莓派的桌面 demo 文件夹中
```
