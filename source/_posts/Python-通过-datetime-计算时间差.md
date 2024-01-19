---
title: Python 通过 datetime 计算时间差
date: 2023-11-28 14:39:00
categories:
- [Code, Python]
tags:
- Python
math: false
---

```python
d1 = datetime.datetime.fromtimestamp(df['time'][start_index] / 1000000000)
d2 = datetime.datetime.fromtimestamp(df['time'][end_index] / 1000000000)
diff_d = d2-d1
print('时间差为 ', diff_d)

# 时间差为  0:12:13.942854
```
