---
title: EMG 相关应用场景
date: 2023-08-28 16:01:09
categories:
- 资料整合
tags: 
- EMG
- 运动科学
math: true
---

### 特征识别

利用 sEMG 特征来识别动作。目前除了 sEMG 外，还有下面方案：

1. 基于计算机视觉的人体姿态识别；
2. 基于惯性测量单元的人体姿态识别。

 sEMG 的主要应用领域为手势识别：

1. [基于表面肌电信号的动作识别（深度学习）](https://github.com/malele4th/sEMG_DeepLearning)；

### 肌肉疲劳

通过肌电疲劳阈值（electromyogram fatigue threshold, $EMG_{FT}$）测量法进行肌肉疲劳分析：

1. [基于表面肌电信号和动作捕捉的上肢运动疲劳分析](https://res.craft.do/user/full/6a1edbd6-3a87-0108-9aee-13ed8d2519a6/4521B14B-F4D8-4E18-9C33-075EB468CAA8_2/0anKdmJXKHOfJLskNNsD0U5WBCELNFkNh3CnGIplcIYz/4521B14B-F4D8-4E18-9C33-075EB468CAA8_2.pdf)；
2. [基于表面肌电信号的康复过程中肌疲劳有效性分析](https://res.craft.do/user/full/6a1edbd6-3a87-0108-9aee-13ed8d2519a6/9AE75833-3F90-40B1-8811-A1590D68338C_2/CW4iRhiNAqLM5DNQwFmOaVIAvLxsi5Az5ce3PaHqKxMz/9AE75833-3F90-40B1-8811-A1590D68338C_2.pdf)。

可以认为是借助 sEMG 以及 $EMG_{FT}$ 算法来测定个体运动过程中的肌肉疲劳拐点。

除了 $EMG_{FT}$ 外，监测肌肉疲劳的方法还有：

1. 通气阈值（ventilator threshold, VT）；
2. 基于计算机视觉的人体姿态识别；
3. 基于惯性测量单元的人体姿态识别（IMU）；
4. 乳酸阈值(AT)。

### 肌肉激活程度

参考：[如何用 EMG（肌電圖）研究挑選最有效的訓練動作?](https://vintraining.medium.com/如何用-emg-肌電圖-研究-挑選最有效的訓練動作-933d1ab20321)

每块肌肉都有相应的「最大自主等长收缩（maximal voluntary isometric contraction, MVIC）」，可通过 EMG 振幅表示。而每个动作都有相应的募集肌肉，以及肌肉的激活程度 %MVIC。例如 Distefano 等人研究了健康自愿者的臀大肌和臀中肌肌电图振幅，这些自愿者进行了 12 种不同难度的下肢力量训练。每个训练重复四次求平均值用于分析。侧卧髋部外展对臀中肌的激活最大，为 81%MVIC。单肢深蹲和单肢硬拉可有效激活臀中肌（分别是 64% 和 58%MVIC）和臀大肌（分别为 59% 和 59%MVIC）。

但不是说动作募集肌肉的 %MVIC 越高，动作就越能刺激相关肌肉成长。还要看其他因素：

1. 活动范围（rang of motion, ROM）；
2. 动作在肌肉延展状态下施加张力的大小和时间。

此外还要加以区分概念：最大自主收缩或者最大肌力（maximum voluntary contraction, MVC）。
