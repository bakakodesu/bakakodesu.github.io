---
title: Pandas 数据截取
date: 2023-11-28 14:59:22
categories:
- [Code, Python]
tags:
- Python
math: false
---

知道 index 的情况下，完全可以采用

```python
df_copy = df.copy()
df_cut = df_copy[top:bottom]
```

进行截取操作。
