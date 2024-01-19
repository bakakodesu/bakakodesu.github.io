---
title: Pandas 已知元素，获取其索引
date: 2023-11-28 15:06:43
categories:
- [Code, Python]
tags:
- Python
math: false
---

例如，知道其时间戳，找到 index

```python
print(df[df['time'].isin([timestamp])])
print(df[df['time'].isin([timestamp])].index)
print(df[df['time'].isin([timestamp])].index[0])
```

第一个 print 为

```
                     time    ecg  hr  rr  marker
1444  1700624323518685002 -0.356 NaN NaN     NaN
```

第二个 print 为

```
Index([1444], dtype='int64')
```

第三个 print 为

```
1444
```
