---
title: Pandas List 转换为 Series
date: 2023-11-20 17:28:15
categories:
- [Code, Python]
tags:
- Python
math: false
---

手上有 ecg 信号数组。需要转换成相应的格式：

```
0        0.000763
1       -0.000610
2        0.000763
3        0.002289
4        0.005341
           ...   
50677    0.005798
50678    0.003357
50679    0.008392
50680    0.010681
50681    0.008240
Name: ECG, Length: 50682, dtype: float64
```

type 后可以发现该格式为：

```
<class 'pandas.core.series.Series'>
```

于是可以

```python
ecg = pd.Series(ecg, name='ECG')
```

这样就将 List 转换成了 Series
