---
title: Pandas 创建并插入数据
date: 2023-11-28 15:27:52
categories:
- [Code, Python]
tags:
- Python
math: false
---

```python
ecgs = [10]
times = [10]
reps = [10]
hrs = [10]

# 创建df
dataframe = pd.DataFrame({'time': times, 'ecg': ecgs, 'rep': reps})

# 插入
new_df = dataframe.copy()
new_df['hr'] = hrs
new_df.to_csv('data.csv', index=False)
```
