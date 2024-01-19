---
title: Pandas 遍历 dataframe 中的 item
date: 2023-11-28 15:33:58
categories:
- [Code, Python]
tags:
- Python
math: false
---

数据格式为

| time                | ecg    | rep  |
| ------------------- | ------ | ---- |
| 1700624658822114462 | -0.157 | 6    |

### 采用 df.iterrows()

```python
for item in df1.iterrows():
    print(item)
```

输出内容为

```
(14017, time    1700624658822114462
ecg     -0.157
rep     6
Name: 14017, dtype: float64)
```

若 `print(item[0])` 则显示

```
14017
```

若 `print(item[0])` 则显示

```
time    1700624658822114462
ecg    	-0.157
rep     6
Name: 14216, dtype: float64
```

### 采用 df.itertuples()

```python
for item in df1.itertuples():
    print(item)
```

输出内容为

```
Pandas(Index=14216, time=1700624658822114462, ecg=-0.157, rep=6)

print(item[0]) -> 14216
print(item[1]) -> 1700624658822114462
print(item[2]) -> -0.157
print(item[3]) -> 6
```

### 采用 df1.items()

```python
for item in df1.items():
    print(item)
```

输出内容为（按列输出）

```
('time', 0        1700624538840442752
1        1700624538943768244
2        1700624539047093736
3        1700624539150419228
4        1700624539253744720
                ...         
14212    1700624658791314974
14213    1700624658799014846
14214    1700624658806714718
14215    1700624658814414590
14216    1700624658822114462
Name: time, Length: 14217, dtype: int64)

('ecg', 0       -0.004
1        0.044
2        0.059
3        0.007
4       -0.086
         ...  
14212   -0.376
14213   -0.310
14214   -0.236
14215   -0.189
14216   -0.157
Name: ecg, Length: 14217, dtype: float64)

('rep', 0        4
1        4
2        4
3        4
4        4
        ..
14212    6
14213    6
14214    6
14215    6
14216    6
Name: rep, Length: 14217, dtype: int64)
```

