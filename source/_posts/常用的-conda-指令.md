---
title: 常用的 conda 指令
date: 2023-04-09 11:24:07
categories:
- 随笔
tags:
- Python
math: false
---

```cmd
conda env list			查看环境
conda list					查看安装包

conda create -n tensorflow python=3.8.5		创建新环境
conda activate tensorflow		激活
conda remove -n tensorflow --all		删除
```

重命名。conda 其实没有重命名指令，实现重命名是通过克隆完成的，分两步：
1、先克隆一份旧环境，克隆过程中重新命名
2、删除旧环境

```cmd
# 第一步：
conda create -n pytorch --clone tensorflow
# 第二步：
conda remove -n tensorflow --all
```

