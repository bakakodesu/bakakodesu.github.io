---
title: 通过 IMU 绘制物体移动的空间轨迹
date: 2023-08-03 12:59:32
categories:
- 资料整合
tags:
- IMU
- 传感器
math: true
---

一般 IMU 由加速度传感器、陀螺仪组成，也叫 6 轴 IMU。还可以外加一个磁力计，构成 9 轴 IMU。关于加速度传感器和陀螺仪的工作原理，可以参考：

1. [加速度传感器的原理和应用-手机翻转、失重检测、运动检测、位置识别](https://blog.csdn.net/yueqian_scut/article/details/50708073)；
2. [MEMS 三轴加速计、三轴陀螺仪、三轴磁力计）6轴IMU+磁力计，9轴传感器讲解](https://blog.csdn.net/KYJL888/article/details/112802532)；
3. [IMU 测量模型](https://xiaotaoguo.com/p/imu-model/)。

加速度传感器测得加速度（acc）积分得到速度，再积分得到位移：

1. [Gyroscope and Accelerometer Calibration with Raspberry Pi](https://makersportal.com/blog/calibration-of-an-inertial-measurement-unit-imu-with-raspberry-pi-part-ii)；
2. [【翻译】利用加速度求解位置的算法——三轴传感器](https://www.cnblogs.com/cposture/p/4378922.html)。

陀螺仪测得角速度（gyr）积分得到姿态：

1. [三维旋转：欧拉角、四元数、旋转矩阵、轴角之间的转换](https://zhuanlan.zhihu.com/p/45404840)；
2. [四元数微分方程的推导和解算实现](https://zhuanlan.zhihu.com/p/103623879)；
3. [三维旋转笔记:欧拉角/四元数/旋转矩阵/轴角-记忆点整理](https://www.zhoulujun.cn/html/theory/Mathematics/Geometry/8149.html)；
4. [四元数及姿态解算 Mahony 算法](https://www.cnblogs.com/TIANHUAHUA/p/8031606.html)。

所以可以通过加速度传感器+陀螺仪的组合得到比较准确姿态，在去除重力加速度分量影响下二次积分可以得到位置。这种基于 IMU 测量数据进行位置、速度解算的方法可简称惯性导航（惯导）解算：

1. [IMU与惯性导航基础知识介绍](https://zhaoxuhui.top/blog/2022/02/11/basic-notes-on-imu-and-inertial-navigation.html#32-mems加速度计的误差特性)；
2. [多传感器融合定位理论基础（五）：惯性导航解算](https://zhuanlan.zhihu.com/p/131342530)；
3. [基于 IMU 的惯性导航解算及误差分析](https://xiaotaoguo.com/p/imu-pose-update/)。

其中的难点在于：

1. 姿态不准导致重力影响去除不了；
2. MEMS 器件本身的零点漂移不能去除。

这导致使用 IMU 定位时，始终存在不可避免的漂移，且漂移会随着积分越来越大。所以需针对具体应用设计相应的处理算法。目前使用较多的**步态定位**。

1. [Oscillatory-Motion-Tracking-With-x-IMU](https://github.com/xioTechnologies/Oscillatory-Motion-Tracking-With-x-IMU)，Matlab开源项目；
2. [Gait-Tracking-With-x-IMU](https://github.com/xioTechnologies/Gait-Tracking-With-x-IMU)，Matlab开源项目；
3. [Gait-Tracking-With-x-IMU](https://github.com/italogsfernandes/Gait-Tracking-With-x-IMU)，Python开源项目；
4. [Gait Tracking with x-imu Python](https://github.com/daehwa/Gait-Tracking-With-x-IMU-Python)，Python开源项目；
5. [Gait-Tracking](https://github.com/xioTechnologies/Gait-Tracking)，Python开源项目。

就目前来说，第 1 个项目的算法效果最好，这是 [效果展示](https://www.zhihu.com/zvideo/1361803858457579522)。但需要注意的是：

1. Script 中 samplePeriod 需要与传感器采样频率一致；
2. 注意单位转换，我这里传感器采集的角速度单位为rad/s，但是项目中角速度的单位为deg/s。两者的转化关系为：
   $$
   π/180*deg = rad
   $$
3. 算法对于 IMU 的放置和运动模式都做了要求。事实上，对于简单、低频的动作，算法都能够绘制出较为精准的空间运动轨迹。但是对于复杂、高频的动作，算法没有表现出很好的兼容性，数据漂移很严重。
