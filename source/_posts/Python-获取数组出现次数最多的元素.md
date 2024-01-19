---
title: Python 获取数组出现次数最多的元素
date: 2023-08-07 16:28:01
categories:
- [Code, Python]
tags:
- Python
math: false
---

```python
a = [1, 2, 3, 4, 2, 3, 2]
max_label = max(a, key=a.count)
print(max_label)
# output: 2
```
