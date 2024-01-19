---
title: Python 导出传感器数据到 .csv 文件
date: 2023-08-03 18:12:16
categories:
- [Code, Python]
tags:
- Python
math: false
---

导出传感器的数据一共分为两步：

1. 构建数据结构；
2. 导出数据到 .csv 文件。

### 1.构建数据结构

一般来说，传感器采集的数据为时间序列数据。需要导出的数据一般为：

```python
times = [t1, t2, t3 ... tn]
value_A = [a1, a2, a3 ... an]
value_B = [b1, b2, b3 ... bn]
value_C = [c1, c2, c3 ... cn]
```

需要构建一个二维数组：

```python
import numpy as np

export_data = np.zeros(n, 4)									# 构建一个 shape = (数据长度, 数据种类) 的 numpy 数组
export_data[::, 0] = times								        # times 替换第 0 列。[0, ::]就是行
export_data[::, 1] = value_A
export_data[::, 2] = value_B
export_data[::, 3] = value_C

# 多此一举的操作
# export_data[0, ::] = times
# export_data[1, ::] = value_A
# export_data[2, ::] = value_B
# export_data[3, ::] = value_C
# export_data = export_data.T									# 将矩阵的行列转置
```

此时 export_data 的结构是这样的：

```python
[ t1 a1 b1 c1
  t2 a2 b2 c2
  t3 a3 b3 c3
     .....
  tn an bn cn ]
```

### 2.导出数据

导出 .csv 需要用到 pandas 库。

```python
import pandas as pd

column = ['Time', 'ValueA', 'ValueB', 'ValueC']										    # 表头
export_csv = pd.DataFrame(columns=column, data=export_data)
export_csv.to_csv('path/name.csv', index=False)										    # index=Ture 的话，会默认生成一列 index 索引列。记住文件名后面要带.csv

# 不需要表头
# export_csv = pd.DataFrame(data=export_data)
# export_csv.to_csv('path/name.csv', header=None, index=False)
```
