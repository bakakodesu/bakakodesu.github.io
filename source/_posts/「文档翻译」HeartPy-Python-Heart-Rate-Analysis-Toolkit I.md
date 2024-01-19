---
title: 「文档翻译」HeartPy - Python Heart Rate Analysis Toolkit I
date: 2023-11-06 15:46:05
categories:
- [Code, Python]
tags:
- Python
- 心率
math: false
---

文档地址[HeartPy - Python Heart Rate Analysis Toolkit](https://github.com/paulvangentcom/heartrate_analysis_python#heartpy---python-heart-rate-analysis-toolkit)

# HeartPy - Python 心率分析工具包

欢迎使用 HeartPy，Python 心率分析工具包的文档。该工具包旨在处理使用 PPG 数据。

- 该工具包是在海牙举行的 Humanist 2018 会议上展示的（[请参阅此处的论文](https://www.researchgate.net/publication/325967542_Heart_Rate_Analysis_for_Human_Factors_Development_and_Validation_of_an_Open_Source_Toolkit_for_Noisy_Naturalistic_Heart_Rate_Data)）。
- 有关该功能的技术论文[可在此处获得](http://doi.org/10.13140/RG.2.2.24895.56485)；

## 示例

如果您正在寻找一些有关如何开始使用 HeartPy 的动手示例，请查看以下链接！这些笔记本展示了如何使用 HeartPy 处理各种分析任务，包括智能手表数据、智能戒指数据、常规 PPG 和常规（非常嘈杂）心电图。笔记本有时无法通过 github 引擎呈现，因此要么在本地打开它们，要么使用 [nbviewer]( https://nbviewer.jupyter.org/ ) 之类的在线查看器。

我们建议您按以下顺序阅读笔记：

- [1. 分析光电容积描记（PPG）信号](https://github.com/paulvangentcom/heartrate_analysis_python/blob/master/examples/1_regular_PPG/Analysing_a_PPG_signal.ipynb)，一个使用内置示例开始使用 HeartPy 的笔记。
- [2. 分析心电图（ECG）信号](https://github.com/paulvangentcom/heartrate_analysis_python/blob/master/examples/1_regular_ECG/Analysing_a_regular_ECG_signal.ipynb)，用于处理 HeartPy 和典型心电图数据的笔记。
- [3. Analyzing smartwatch data](https://github.com/paulvangentcom/heartrate_analysis_python/blob/master/examples/3_smartwatch_data/Analysing_Smartwatch_Data.ipynb)，一个分析智能手表低分辨率 PPG 数据的笔记。
- [4. 智能环数据分析](https://github.com/paulvangentcom/heartrate_analysis_python/blob/master/examples/smartring_data/Analysing_Smart_Ring_Data.ipynb)，一个分析智能环 PPG 数据的笔记本。
- [5. 分析嘈杂的心电图数据](https://github.com/paulvangentcom/heartrate_analysis_python/blob/master/examples/noisy_ECG/Analysing_Noisy_ECG.ipynb)，使用来自 MIT-BIH 噪声的数据处理非常嘈杂的心电图数据的高级笔记压力测试数据。
- [6. Colorblind mode - How To and Styles](https://github.com/paulvangentcom/heartrate_analysis_python/blob/master/examples/6_colorblind_mode/Colorblind_mode.ipynb)

### 示例1.分析 PPG 信号

本笔记适合那些不了解 HeartPy 的人。 它包含一些可用 API 的简单示例。

在本笔记中，我们将介绍如何使用 HeartPy 分析常规 PPG 信号。

为此，我们将使用与 HeartPy 一起打包的示例数据集。

HeartPy 附带一个函数 `load_exampledata()` 可以加载提供的数据集。 

0：一个短的、非常干净的 PPG 信号，以 100.0 Hz 采样
1：稍长（约 2 分钟）的 PPG 信号，前三分之一丢失信号，其余信号出现随机噪声尖峰
2：在驾驶模拟器中驾驶时使用食指上的脉冲传感器和 Arduino 记录的长（约 11.5 分钟）PPG 信号

```python
#First let's import
import heartpy as hp
import matplotlib.pyplot as plt

#first let's load the clean PPG signal
data, timer = hp.load_exampledata(0)

#and visualise
plt.figure(figsize=(12,4))
plt.plot(data)
plt.show()
```

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/「文档翻译」HeartPy-Python-Heart-Rate-Analysis-Toolkit1.png)

运行 `hp.process()` 时，会返回两个字典：working_data（包含您可能想要访问的工作数据，例如峰值位置和峰峰值间隔）和 measures（包含计算的输出测量值）。

在文档中，我们将它们缩写为「wd」和「m」。

现在您可能想要可视化分析结果。

Heartpy 附带了一个函数 `hp.plotter(wd, m)` 来完成此操作。该函数要求为其提供工作数据和测量字典。

如果在调用 `hp.plotter(wd, m)` 之前向 matplotlib 指定图形大小等参数，则将使用指定的图形大小。

```python
#run the analysis
wd, m = hp.process(data, sample_rate = 100.0)

#set large figure
plt.figure(figsize=(12,4))

#call plotter
hp.plotter(wd, m)

#display measures computed
for measure in m.keys():
    print('%s: %f' %(measure, m[measure]))
```

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/「文档翻译」HeartPy-Python-Heart-Rate-Analysis-Toolkit2.png)

```json
bpm: 58.898848
ibi: 1018.695652
sdnn: 65.760061
sdsd: 34.866925
rmssd: 64.737231
pnn20: 0.863636
pnn50: 0.409091
hr_mad: 40.000000
sd1: 45.758077
sd2: 82.926832
s: 11921.000816
sd1/sd2: 0.551789
breathingrate: 0.161095
```

That looks nice, and we ran into no problems.

Let's move on to the second included dataset and explore it first:

```python
data, timer = hp.load_exampledata(1)

plt.figure(figsize=(12,4))
plt.plot(data)
plt.show()
```

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/「文档翻译」HeartPy-Python-Heart-Rate-Analysis-Toolkit3.png)

这是故意制作的一个更棘手的信号。一开始没有信号。信号开始后，当在记录时用力移动传感器时，会出现一些噪音尖峰。这模仿了在野外录制时可能发生的情况，以及参与者移动并意外拉动传感器电缆时可能发生的情况。

HeartPy 旨在开箱即用地处理此类事情。 我们先看一下例子：

```python
sample_rate = hp.get_samplerate_mstimer(timer)

wd, m = hp.process(data, sample_rate)

#plot
plt.figure(figsize=(12,4))
hp.plotter(wd, m)

#display measures computed
for measure in m.keys():
    print('%s: %f' %(measure, m[measure]))
```

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/「文档翻译」HeartPy-Python-Heart-Rate-Analysis-Toolkit4.png)

```json
bpm: 62.376304
ibi: 961.903744
sdnn: 64.613519
sdsd: 29.871930
rmssd: 57.069746
pnn20: 0.797297
pnn50: 0.486486
hr_mad: 47.010333
sd1: 40.344402
sd2: 81.621935
s: 10345.227446
sd1/sd2: 0.494284
breathingrate: 0.155994
```

请注意，我们从计时器列计算了 sample_rate（以毫秒为单位）。这一点很重要，因为我们不知道信号的采样率是多少。所有测量都取决于了解采样率。

HeartPy 附带两个函数：`hp.get_samplerate_mstimer()`，用于基于 ms 计时器计算采样率；`hp.get_samplerate_datetime()`，用于基于日期时间值中的列计算 sample_rate。

您之前已经了解了我们如何使用 mstimer，现在让我们看一下使用日期时间字符串对时间进行编码的野外记录：

```python
data, timer = hp.load_exampledata(2)

print(timer[0])
```

```json
2016-11-24 13:58:58.081000
```

When computing the sample rate we need to give `get_samplerate_datetime()` the format of the string (by default it expects HH:MM:SS.ms):

```python
sample_rate = hp.get_samplerate_datetime(timer, timeformat='%Y-%m-%d %H:%M:%S.%f')

print('sample rate is: %f Hz' %sample_rate)
```

```json
sample rate is: 100.419711 Hz
```

Now that we know all that, running the analysis is the same as before:

```python
wd, m = hp.process(data, sample_rate, report_time = True)

#plot
plt.figure(figsize=(12,4))
hp.plotter(wd, m)

#let's zoom in on a bit
plt.figure(figsize=(12,4))
plt.xlim(20000, 30000)
hp.plotter(wd, m)

#display measures computed
for measure in m.keys():
    print('%s: %f' %(measure, m[measure]))
```

```json
Finished in 0.248689 sec
```

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/「文档翻译」HeartPy-Python-Heart-Rate-Analysis-Toolkit5.png)

![](/Users/bakako/Library/Mobile Documents/com~apple~CloudDocs/blog/图/「文档翻译」HeartPy-Python-Heart-Rate-Analysis-Toolkit/「文档翻译」HeartPy-Python-Heart-Rate-Analysis-Toolkit6.png)

```json
bpm: 97.325149
ibi: 616.490194
sdnn: 57.916589
sdsd: 30.173744
rmssd: 38.181375
pnn20: 0.320281
pnn50: 0.086345
hr_mad: 29.874613
sd1: 26.997110
sd2: 75.178572
s: 6376.189507
sd1/sd2: 0.359106
breathingrate: 0.258103
```

