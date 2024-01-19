---
title: PyCharm 获取 Conda 环境列表失败，报错 error code 1 的解决办法
date: 2023-07-27 17:34:23
categories:
- [环境配置, PyCharm]
- [环境配置, Anaconda]
tags:
- Python
math: false
---

通常来说，在设置 Python Interpreter 时，Conda executable 的路径为`anaconda\Scripts\conda.exe`。但是我在给同事部署环境填入该路径，且路径下也确实有对应文件存在，却报错`error code 1`。

**解决方案：用这个路径`anaconda\Library\bin\conda.bat`可解决问题。**

这让我想起第一次在 Windows 上部署 Conda 时，默认可执行文件是自动探测的，刚刚好是`anaconda\Library\bin\conda.bat`。不过那个时候教程让我用`anaconda\Scripts\conda.exe`，我选择了相信教程。当然教程没问题，我也成功部署了。只是没想到给同事部署时，教程的方法会报错。

另外 Mac 的可执行文件是自动探测、默认填写好的，不需要自己手动填写。
