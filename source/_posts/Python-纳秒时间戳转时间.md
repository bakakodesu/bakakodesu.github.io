---
title: Python 纳秒时间戳转时间
date: 2023-11-28 14:48:49
categories:
- [Code, Python]
tags:
- Python
math: false
---

关于数量级

```\
1 秒
1, 000 毫秒
1, 000, 000 微秒
1, 000, 000, 000 纳秒

对于一个纳秒时间戳：
1700621512995758338. 纳秒
1700621512995758.338 微秒
1700621512995.758338 毫秒
1700621512.995758338 秒
```

转换代码

```python
import datetime

#假设纳秒时间戳为 1635678377992198863
nano_second_timestamp = 1635678377992198863

# 将纳秒时间戳除以一亿得到秒级时间戳
second_timestamp = nano_second_timestamp / 1000000000

# 将秒级时间戳转换为datetime对象
d = datetime.datetime.fromtimestamp(second_timestamp)

# 将datetime对象转换为指定格式的时间字符串
formatted_time = d.strftime('%Y-%m-%d %H:%M:%S.%f')

print(formatted_time) # 输出：'2021-10-31 15:06:17.992199'
```

在上述代码中，最后的时间字符串格式为  `'%Y-%m-%d %H:%M:%S.%f'`，其中 %Y 代表年份，%m 代表月份，%d 代表日期，%H 代表小时，%M 代表分钟，%S 代表秒，%f 代表微秒（注意，因为 datetime 对象的微秒值是范围在 0~999999 之间，所以需要对纳秒时间戳进行转换后，取后六位代表微秒）。

