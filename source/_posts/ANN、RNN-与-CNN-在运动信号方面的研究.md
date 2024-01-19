---
title: ANN、RNN 与 CNN 在运动信号方面的研究
date: 2023-12-29 11:47:07
categories:
- 文章
tags:
- 深度学习
- 运动科学
- 心率
- IMU
- EMG
math: false
---

### ANN（MLP）

这里将 SVM 和 Random Forest 也包含在内。

#### 1.《Influence mechanism of running sportswear fatigue based on BP neural network》

ANN，手动提取 EMG 信号特征预测疲劳。

#### 2.《A Novel Method for Classification of Running Fatigue Using Change-Point Segmentation》

Random Forest，手动提取 EMG 信号特征预测疲劳。

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/ANN、RNN-与-CNN-在运动信号方面的研究12.png)

#### 3.《Sport-induced fatigue detection in gait parameters using inertial sensors and support vector machines》

SVM，手动提取 IMU 信号特征预测疲劳。

### RNN

#### 1.《Sports fatigue detection based on deep learning》

BiLSTM，手动提取 ECG 信号特征预测 RPE。

该文献不是直接采用原始 ECG 信号进行模型训练，而是先手动提取了 ECG 信号特征（HRV）。

可以看到原始信号和提取特征后的准确率：

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/ANN、RNN-与-CNN-在运动信号方面的研究9.png)

文献还对比了 Random Forest、SVM 与 ANN 下面为准确率：

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/ANN、RNN-与-CNN-在运动信号方面的研究10.png)

####  2.《Heart Rate Variability-Based Subjective Physical Fatigue Assessment》

LightGBM，手动提取 ECG 信号特征预测 RPE。

这篇文献是采用 LightGBM 处理时间序列，所以放在了 RNN 这里。本篇文献除了采用 LightGBM 外，还对比了 DT、KNN 与 SVM 的准确率：

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/ANN、RNN-与-CNN-在运动信号方面的研究11.png)

### CNN

#### 1.《Real-Time Detection of Acute Cognitive Stress Using a Convolutional Neural Network From Electrocardiographic Signal》

CNN，通过 CNN 提取 ECG 信号特征预测急性认知压力。

文献使用了十层的简单 CNN 结构。

- 第一层是图像输入层。其大小为799×1×1，等于输入频带的大小；
- 第二层是卷积层，由六个 4×1×1 的滤波器组成，步幅为 1×1；
- 接下来是批量归一化层、ReLU 层和概率为 0.5 的 dropout 层；
- 接下来的三层是两个全连接层和它们之间的批量归一化层。两个全连接层的单元数量分别为十个和两个；
- 最后两层是 softmax 层和分类输出层，它们都有两个单元。

#### 2.《Cardiologist-Level Arrhythmia Detection with Convolutional Neural Networks》

CNN，通过 CNN 提取 ECG 信号特征预测心律失常。

CNN 网络大致的结构都可以用下面图片展示：

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/ANN、RNN-与-CNN-在运动信号方面的研究8.png)

#### 3.《基于表面肌电信号能量核相图的卷积神经网络人体手势识别算法》

CNN，通过 CNN 提取 EMG 信号特征预测手势。

### 组合模型

#### 1.《A Deep Learning Approach for Fatigue Prediction in Sports Using GPS Data and Rate of Perceived Exertion》

CNN-GRU，通过 CNN 提取 IMU 信号特征，然后通过 GRU 训练预测 RPE。

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/ANN、RNN-与-CNN-在运动信号方面的研究7.png)

#### 2.《基于卷积长短时记忆网络的心律失常分类方法》

CNN-LSTM，该文献通过 CNN 提取 ECG 特征后，并不是直接交给 FC 层进行分类，而是再经过 LSTM 后再交给 FC 层进行 Softmax 分类。

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/ANN、RNN-与-CNN-在运动信号方面的研究1.png)

#### 3.《Dynamic gesture recognition based on LSTM-CNN》

与上面文献恰好相反，该文献的组合为 LSTM-CNN 识别 EMG 信号对应的手势。

模型由 2 个 LSTM 层、2 个一维卷积层和 1 个输出层组成。首先，通过 LSTM 层传递输入信号来提取时间信息。使用 2 个 LSTM 层，每个 LSTM 层有 52 个单元，每个单元有 64 个隐藏层。LSTM层的输出信号被输入到擅长特征提取的CNN层。具体来说，使用 2 个卷积层，第一层使用 65 个 3·1 卷积核，第二层卷积层使用 30 个 3·1 滤波器。

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/ANN、RNN-与-CNN-在运动信号方面的研究6.png)

#### 4.《基于深度学习并行网络模型的心律失常分类方法》

这篇文献的网络比较复杂。

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/ANN、RNN-与-CNN-在运动信号方面的研究2.png)

一共采用了 3 种网络 DenseNet+BiLSTM+ECANet，最后采用 Softmax 对 ECG 信号进行分类。

##### DenseNet（CNN）

密集连接卷积神经网络，Densely connected convolutional network

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/ANN、RNN-与-CNN-在运动信号方面的研究3.png)

##### BiLSTM（RNN）

双向长短时记忆网络，Bidirectional long short-term memory network

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/ANN、RNN-与-CNN-在运动信号方面的研究4.png)

##### ECANet

高效通道注意力网络，Efficient channel attention network

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/ANN、RNN-与-CNN-在运动信号方面的研究5.png)

#### 5.《融合 CNN 和 BiLSTM 的心律失常心拍分类模型》

本文献融合了 CNN 和 BiLSTM 的神经网络。将经过正则化的大尺度心拍输入 Muti-BiLSTM 网络中，提取一维时序数据的全局特征；将经过正则化的小尺度心拍输入 CNN 网络提取心拍的局部特征。将大尺度特征和局部特征经过拼接后的向量输入到 3 层全连接网络中。全连接层的激活函数都是指数线性单元(exponential linear units, ELU)，最后通过 softmax 的激活函数实现心拍的分类。

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/ANN、RNN-与-CNN-在运动信号方面的研究15.png)

这篇文献倒是没有上篇文献那么复杂。虽然同样是分成了 2 个网络：

##### CNN

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/ANN、RNN-与-CNN-在运动信号方面的研究13.png)

##### BiLSTM

利用多层的 BiLSTM（Multi-BiLSTM）网络构成一个全局特征提取器。其中每一层都包括一个前向的 LSTM 和后向的 LSTM 层，最后一个双边 LSTM 紧跟着一个全连接层。

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/ANN、RNN-与-CNN-在运动信号方面的研究14.png)
