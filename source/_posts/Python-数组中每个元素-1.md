---
title: Python 数组中每个元素+1
date: 2023-11-28 14:34:13
categories:
- [Code, Python]
tags:
- Python
math: false
---

方法一

```python
a = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
a = map(lambda x:x+1,a)
```

方法二

```python
a = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
a = [item+1 for item in a]
```

