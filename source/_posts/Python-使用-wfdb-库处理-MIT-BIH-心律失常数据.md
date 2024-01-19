---
title: Python 使用 wfdb 库处理 MIT-BIH 心律失常数据
date: 2024-01-04 11:37:22
categories:
- [Code, Python]
tags:
- 心率
- Python
math: false
---

数据源

- 原始网页 [MIT-BIH Arrhythmia Database](https://www.physionet.org/content/mitdb/1.0.0/#files-panel)

- kaggle [MIT-BIH Arrhythmia Database](https://www.kaggle.com/datasets/mondejar/mitbih-database/data?select=101.csv)

### 数据解读

主要内容来自[MIT-BIH心律失常数据库详解](https://blog.csdn.net/Mrm_cong/article/details/104668271)

心律失常数据库每一个数据记录都包含三个文件，“.hea”、“.dat”和“.atr”
**.hea** 为头文件，记录文件名、导联数、采样率、数据点数
**.dat** 为数据文件，采用212格式进行存储。
**.atr** 为注释文件，记录了心电专家对相应的心电信号的诊断信息。

在大多数记录中，第一条信号是MLII型（通过将电极置于胸部而获得），第二条信号通常是V1（偶尔是V2或V5，在一个实例中是V4）。现将各记录中导联类型统计如下：

1. MLII、V1 : 101、105、106、107、108、109、111、112、113、115、116、118、119、121、122、200、201、202、214、215、217、219、220、221、222、223、228、230、231、232、233、234
2. MLII、V2 ： 103、117
3. MLII、V4 ： 124
4. MLII、V5 ： 100、123
5. V5、MLII ： 114
6. V5、V2 ：102、104

具体可看 [Tables of beats and rhythms](https://www.physionet.org/physiobank/database/html/mitdbdir/tables.htm)

### wfdb

主要内容来自 [基于窦性心律的阵发性房颤预测算法研究](https://blog.csdn.net/m0_55245520/article/details/130115513)

原生 python 波形数据库（wfdb）包。用于读取、写入和处理 WFDB 信号和注释的工具库。

此包的核心组件基于原始 wfdb 规范。此包不包含与原始 wfdb 包完全相同的功能。它的目标是用用户友好的 api 实现尽可能多的核心功能。随着时间的推移，还增加了其他有用的生理信号处理工具。

#### 读取 .hea 文件

```python
import wfdb
record = wfdb.rdheader('../data/abnormal/04015')
display(record.__dict__)
```

结果

```python
'''
{'record_name': '04015', 
'n_sig': 2, 
'fs': 250,
'counter_freq': None, 
'base_counter': None, 
'sig_len': 9205760, 
'base_time': datetime.time(9, 0), 
'base_date': None, 
'comments': [], 
'sig_name': ['ECG1', 'ECG2'], 
'p_signal': None, 
'd_signal': None, 
'e_p_signal': None, 
'e_d_signal': None, 
'file_name': ['04015.dat', '04015.dat'], 
'fmt': ['212', '212'], 
'samps_per_frame': [1, 1], 
'skew': [None, None], 
'byte_offset': [None, None], 
'adc_gain': [200.0, 200.0], 
'baseline': [0, 0], 
'units': ['mV', 'mV'], 
'adc_res': [12, 12], 
'adc_zero': [0, 0], 
'init_value': [-55, -42], 
'checksum': [-27172, -28460], 
'block_size': [0, 0]}
'''
```

比较有用的部分：

- fs：采样频率；
- n_sig：信号通道数；
- sig_len：信号长度；
- p_signal：模拟信号值，储存形式为ndarray或者是list；
- d_signal：数字信号值，储存形式为ndarray或者是list。

#### 读取标注 .atr/.qrs/.qrsc

```python
record=wfdb.rdann('../data/abnormal/04015', 'atr' ) 
print(record.__dict__)
```

结果：

```python
'''
{
'record_name': '04015', 
'extension': 'atr', 
'sample': array([30, 102584, 119604, 121773, 122194, 133348, 166857, 1096245, 1098054, 1135296, 1139595, 1422436, 1423548, 1459277, 1460416], dtype=int64), 
'symbol': ['+', '+', '+', '+', '+', '+', '+', '+', '+', '+', '+', '+', '+', '+', '+'], 
'subtype': array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]), 
'chan': array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]), 'num': array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]), 
'aux_note': ['(N', '(AFIB', '(N', '(AFIB', '(N', '(AFIB', '(N', '(AFIB', '(N', '(AFIB', '(N', '(AFIB', '(N', '(AFIB', '(N'], 
'fs': 250, 
'label_store': None, 
'description': None, 
'custom_labels': None,
'contained_labels': None, 
'ann_len': 15}
'''
```

常用到的部分:

- sample：专家标注的信号发生变化的位置
- symbol：信号发生变化的原因

### 制作数据集

```python
import numpy as np
import wfdb


def read_db(n, x, y):
    record = wfdb.rdrecord('/Users/bakako/Downloads/mit-bih-arrhythmia-database-1.0.0/' + n, channel_names=['MLII'])
    # print(record.__dict__)
    data = record.p_signal   # p_signal 为模拟信号，d_signal 为数字信号
    data = data.flatten()    # 降维

    annotation = wfdb.rdann('/Users/bakako/Downloads/mit-bih-arrhythmia-database-1.0.0/' + n, extension='atr')
    R_location = annotation.sample  # R 波所在的采样点, 专家标注的信号发生变化的位置
    R_type = annotation.symbol      # R 波的 type, 信号发生变化的原因
    # print(R_location)
    # print(R_type)

    # 抛掉一些心拍，保证构建数据集时不会超出数组边界
    i = 5
    j = len(R_type) - 5

    while i < j:
        try:
            label = R_type[i]
            # 基于经验值，基于 R 峰向前取 100 个点，向后取 200 个点
            # 如果不抛掉前 5 和后 5 心拍，取 feature 时可能会超出数组边界
            feature = data[R_location[i] - 100:R_location[i] + 200]
            x.append(feature)
            y.append(label)
            i += 1
        except ValueError:
            i += 1

    print(n + 'over')


def load_dataset():
    number = ['100', '101', '103', '105', '106', '107', '108', '109', '111', '112', '113', '114', '115',
              '116', '117', '119', '121', '122', '123', '124', '200', '201', '202', '203', '205', '208',
              '210', '212', '213', '214', '215', '217', '219', '220', '221', '222', '223', '228', '230',
              '231', '232', '233', '234']

    features = []
    labels = []

    for n in number:
        read_db(n, features, labels)

    # row 行
    # column 列
    row = len(features)
    print(row)

    features = np.array(features)
    features = features.reshape(row, 300)
    print(features.shape)
    print(len(labels))
    # features 是二维 numpy 数组，labels 是 list

load_dataset()
```

