---
title: Python 关键字 Nonlocal 与 Global
date: 2023-08-07 16:54:21
categories:
- [Code, Python]
tags:
- Python
math: false
---

global 的用法：

```python
num1 = 0
num2 = 0

def change_num():
  global num1
  num1 = 1
  num2 = 1

change_num()
print(num1, num2)
# output: 1, 0
# 不允许直接修改全局变量(数组、字典除外)，需要添加 global 关键字
```



nonlocal 的用法：

```python
num1 = 0
num2 = 0

def change_num():
  num3 = 1
  num4 = 1
  
  def change_num_inner():
    nonlocal num3
    num3 = 2
    num4 = 2
    
  change_num_inner()
  
  return num3, num4

num1, num2 = change_num()
print(num1, num2)
# output: 2, 1
```
