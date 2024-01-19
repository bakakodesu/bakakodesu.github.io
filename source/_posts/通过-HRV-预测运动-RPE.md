---
title: 通过 HRV 预测运动 RPE
date: 2023-12-15 14:56:04
categories:
- 资料整合
tags:
- 深度学习
- 运动科学
math: false
---

通过 HRV 预测 REP 核心文献为 2 篇。

1. 《Heart Rate Variability-Based Subjective Physical Fatigue Assessment》
2. 《Sports fatigue detection based on deep learning》

### 1.HRV 的相关指标

#### 1.1.时域分析

| 名称   | 单位  | 描述                                                       |
| ------ | ----- | ---------------------------------------------------------- |
| meanNN | ms    | 平均相邻正常心跳时间间隔                                   |
| meanHR | 1/min | 平均心率                                                   |
| SDNN   | ms    | 全部正常窦性心搏间期（NN）的标准差                         |
| RMSSD  | ms    | 全程相邻NN间期之差的均方根值                               |
| SDANN  | ms    | 分段先计算心跳间期的平均值，再计算平均间期的标准差         |
| SDNNi  | ms    | 上述标准差的平均值                                         |
| NN50   | count | 每对相邻正常心跳时间间隔，差距超过50毫秒的数目             |
| pNN50  | %     | 间隔差距超过50毫秒的占比                                   |
| HRVTi  | -     | 相邻正常心跳间隔的总个数除以相邻正常心跳间隔直方图的高度   |
| TINN   | ms    | 使用最小方差法，求出全部NN间期的直方图近似三角形底边的宽度 |

目前已知：

1. HRVTi 减小，交感神经活动增强，副交感神经活动下降；

#### 1.2.频域分析

| 名称    | 单位 | 描述       |
| ------- | ---- | ---------- |
| VLF     | ms^2 | 极低频功率 |
| LF      | ms^2 | 低频功率   |
| HF      | ms^2 | 高频功率   |
| LF/HF   | -    | 比值       |
| peakVLF | Hz   |            |
| peakLF  | Hz   |            |
| peakHF  | Hz   |            |

目前已知：

1. LF/HF 减小，反应了交感神经和副交感神经系统活动的均衡状态发生变化；

#### 1.3.非线性分析

| 名称    | 单位 | 描述                                           |
| ------- | ---- | ---------------------------------------------- |
| sampen  | -    | 两个序列在下一点保持相似的条件概率的负自然对数 |
| SD1     | ms   | 庞加莱图短轴                                   |
| SD2     | ms   | 庞加莱图长轴                                   |
| SD1/SD2 | -    |                                                |
| Alpha   | -    | 去波动趋势分析                                 |
| Alpha1  | -    | 短时程参数                                     |
| Alpha2  | -    | 长时程参数                                     |

目前已知：

1. SD1 反应的是短期、瞬时 RR（两个 R 波） 变化；
2. SD2 则是为长期的；
3. SD1/SD2 可代表交感神经与副交感神经的活性；
4. 运动强度越大 Alpha1 越低。

### 2.具体文献

两篇文献均是通过机器学习的方法，实现 HRV 数据预测运动期间的 REP。

前者，也就是《Heart Rate Variability-Based Subjective Physical Fatigue Assessment》，通过 Bruce 测试，具体内容见下：

| Stage | Duration（min） | Speed | Incline（%） |
| ----- | --------------- | ----- | ------------ |
| rest  | 5               | 0     | 0            |
| 1     | 5               | 3     | 5            |
| 2     | 5               | 5     | 5            |
| 3     | 5               | 6.4   | 5            |
| 4     | 5               | 7.8   | 5            |
| 5     | 5               | 10.2  | 5            |
| 6     | 直到力竭        | 11.6  | 5            |

收集测试期间的心跳数据（ECG），并且在每隔阶段结束时，要求汇报 RPE。

这里的 RPE 采用的是 6-20 量表。

但最后会被划分为：

1. 6-10 为轻松；
2. 11-16 为有点累；
3. 17-20 为累。

最终呈现的数据格式为:

```
stage:
	duration:
	speed:
	incline:
heart: []
rpe:
```

后者《Sports fatigue detection based on deep learning》同为 Bruce 测试。

但每个阶段的具体运动速度/坡度/时间有调整，针对男女具体运动内容不同。

每个阶段改为 3 分钟，且**每**分钟都收集一次 RPE。同样采用 6-20 量表，但划分方式有点区别：

1. 6-11 为轻松；
2. 12-16 为有点累；
3. 17-20 为累。

前者在 31 个特征中筛选出 14 个特征，并最终选用 LightGBM 算法。

后者直接确认了 27 个特征，使用 Bi-LSTM 神经网络。

### 3.其它

除了使用 HRV 特征量外，还可以通过卷积（CNN）的方式来提取特征。参考：

1. 《Real-Time Detection of Acute Cognitive Stress Using a Convolutional Neural Network From Electrocardiographic Signal》
2. 《基于表面肌电信号能量核相图的卷积神经网络人体手势识别算法》
