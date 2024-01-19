---
title: Python 绘制数据移动窗口
date: 2023-08-03 16:59:47
categories:
- [Code, Python]
tags:
- Python
math: false
---

```python
import numpy as np

win_size = 500                          # 窗口大小为500
x_data = np.zeros(win_size)
y1_data = np.zeros(win_size)
y2_data = np.zeros(win_size)
y3_data = np.zeros(win_size)

...

def update_data(): 
  # 只展示x轴数据平移过程，y轴同理
  x_data[:-1] = x_data[1:]                # 移动
  x_data[-1] = new_value                  # 将新值写入
  ...

while True:
  update_data()
```
