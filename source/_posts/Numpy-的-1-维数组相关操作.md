---
title: Numpy 的 1 维数组相关操作
date: 2023-12-20 16:57:43
categories:
- [Code, Python]
tags:
- Python
math: false
---

np.array([1,2,3])，既不是行向量，也不是列向量，称为秩 rank 为 1 的 array，如果对其转置，则会得到其本身。

```python
import numpy as np
b = np.array([1,2,3])  # shape=（3，）
b2 = b.reshape((3,1))  # shape=（3，1）
```

当然也可以进行降维操作

```python
b3 = b2.reshape(3, )  # b2.flatten() 都是降维 shape=（3，）
```
