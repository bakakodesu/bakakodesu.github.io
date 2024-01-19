---
title: >-
  Python 在使用 curve_fit 拟合函数时，报错OptimizeWarning: Covariance of the parameters
  could not be estimated
date: 2023-11-06 15:20:26
categories:
- [Code, Python]
tags:
- Python
math: false
---

在使用 Scipy 函数进行曲线拟合时遇到这个问题，协方差矩阵无法正确求出，均为Inf。

**解决方案为给拟合的参数定义一个边界：**

scipy.optimize.curve_fit 是一个用于拟合曲线的函数。它可以帮助我们通过拟合数据点来得到一个函数模型，以便我们能够使用这个模型进行预测和分析。

在某些情况下，我们希望通过对函数参数设置边界来控制拟合结果。这时我们可以使用 curve_fit 函数中的 bounds 参数。

bounds 参数是一个元组，用于指定每个参数的下限和上限。如果一个参数没有边界限制，可以将其设置为负无穷或正无穷。

以下是一个使用 bounds 参数的示例代码：

```python
import numpy as np
from scipy.optimize import curve_fit

def func(x, a, b, c):
    return a * np.exp(-b * x) + c

# 创建模拟数据
xdata = np.linspace(0, 4, 50)
y = func(xdata, 2.5, 1.3, 0.5)
ydata = y + 0.2 * np.random.normal(size=len(xdata))

# 设置参数边界
bounds = ([0, 0, 0], [np.inf, np.inf, np.inf])

# 使用curve_fit拟合数据
popt, pcov = curve_fit(func, xdata, ydata, bounds=bounds)

# 打印拟合结果
print(popt)
```

