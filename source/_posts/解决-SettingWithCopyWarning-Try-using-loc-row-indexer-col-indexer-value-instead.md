---
title: >-
  解决 SettingWithCopyWarning Try using .loc[row_indexer,col_indexer] = value
  instead
date: 2023-11-28 14:22:31
categories:
- [Code, Python]
tags:
- Python
math: false
---

利用 Pandas 向 DataFrame 中插入一列数据时，出现警告：

```
SettingWithCopyWarning:
A value is trying to be set on a copy of a slice from a DataFrame.
Try using .loc[row_indexer,col_indexer] = value instead
```

解决办法：

先 copy 一份数据。

```python
a = a.copy()
a['column'] = Your List
```
