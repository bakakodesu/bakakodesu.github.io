---
title: PyTorch 鸢尾花深度学习
date: 2023-06-23 14:38:59
categories:
- [Code, Python]
tags:
- Python
- 深度学习
math: false
---

### 鸢尾花数据

[Iris Species](https://www.kaggle.com/datasets/uciml/iris)

### 定义鸢尾花类

```python
class Iris:
    def __init__(self, sepal_length, sepal_width, petal_length, petal_width, species):
        self.sepal_length = sepal_length
        self.sepal_width = sepal_width
        self.petal_length = petal_length
        self.petal_width = petal_width
        self.species = species

    # 返回 feature
    def feature(self):
        return [self.sepal_length, self.sepal_width, self.petal_length, self.petal_width]

    # 返回 label
    def label(self):
        if self.species == 'Iris-setosa':
            return 0
        elif self.species == 'Iris-versicolor':
            return 1
        elif self.species == 'Iris-virginica':
            return 2
```



### 定义鸢尾花数据集

```python
class IrisDataset(Dataset):
    def __init__(self, features, labels):
        self.features = features
        self.labels = labels

    # 返回数据集长度
    def __len__(self):
        return len(self.features)

    def __getitem__(self, index):
        item_feature = self.features[index]
        item_label = self.labels[index]
        return item_feature, item_label
```

### 读取 csv 数据构建数据集

```python
def get_data():
    tic = time.time()
    print('读取cvs文件')
    csv_path = '/Users/bakako/Downloads/archive/Iris.csv'
    datasource = pd.read_csv(csv_path)
    datasource_len = len(datasource)

    # 构建数据集矩阵 n 行，4 列

    # [[0. 0. 0. 0.]
    #       ...
    #  [0. 0. 0. 0.]]
    iris_features = np.zeros((datasource_len, 4), dtype=float)
    iris_labels = []

    print('构造数据结构')
    for i in range(datasource_len):
        # 创建 iris 对象
        iris = Iris(datasource.SepalLengthCm[i], datasource.SepalWidthCm[i],
                    datasource.PetalLengthCm[i], datasource.PetalWidthCm[i],
                    datasource.Species[i])

        # iris = Iris(datasource['SepalLengthCm'][i], datasource['SepalWidthCm'][i],
        #             datasource['PetalLengthCm'][i], datasource['PetalWidthCm'][i],
        #             datasource['Species'][i])

        # [[0. 0. 0. 0.]  <- 将元素一行一行进行替换
        #       ...
        #  [0. 0. 0. 0.]]
        iris_features[[i], :] = iris.feature()
        iris_labels.append(iris.label())

    # 把数据集按照 8:2 的比例来划分为训练集和测试集
    train_features, \
        test_features, \
        train_labels, \
        test_labels = train_test_split(iris_features, iris_labels, test_size=0.2)

    print('构造训练集')
    # 将训练集和测试集转换为PyTorch的张量对象并设置数据类型，加上to(device)可以运行在GPU上
    train_dataset = IrisDataset(torch.FloatTensor(train_features),
                                torch.LongTensor(train_labels))
    test_features = torch.FloatTensor(test_features)
    test_labels = torch.LongTensor(test_labels)

    toc = time.time()
    print('Loading Time: ' + str(1000 * (toc - tic)) + 'ms')
    print('')
    return train_dataset, test_features, test_labels
```

### 训练模型

```python
def train_model(dataset, epochs, gpu):
    tic = time.time()
    device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
    model = Model(n_feature=4, n_hidden=40, n_label=3)
    if gpu:
        model.to(device)

    optimizer = torch.optim.SGD(model.parameters(), lr=0.02)
    loss_func = torch.nn.CrossEntropyLoss()
    batch_size = 5  # 每次训练 5 个样本
    dataloader = DataLoader(dataset, batch_size=batch_size, shuffle=True, num_workers=0, pin_memory=True)

    # epoch 为训练次数
    for epoch in range(epochs):
        # 分 120/5 = 24 次训练
        for step, (features, labels) in enumerate(dataloader):
            if gpu:
                features = features.to(device)
                labels = labels.to(device)

            # 前向传播计算输出结果
            output = model(features)
            loss = loss_func(output, labels)
            # 注意 labels 的结构应该是 [0. 0. 0.]，而不是[[0.], [0.], [0.]]
            # loss 必须是[x, x, x] -> y，而不是[x, x, x] -> [y]
            # 否则会报错：0D or 1D target tensor expected, multi-target not supported
            # 如果出现这种情况，可以进行降维 labels = labels.squeeze(1)

            # 反向传播，更新梯度并优化模型参数
            optimizer.zero_grad()
            loss.backward()
            optimizer.step()
            print('Epoch: ', epoch + 1, '| Step: ', step + 1, '| Loss: ', loss.item())

    toc = time.time()
    print('训练结束')
    print('Train time:' + str(1000 * (toc - tic)) + 'ms')
    print('')

    return model
```

### 整体代码

```python
import torch
from torch.utils.data import Dataset, DataLoader
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split
import time


# 定义鸢尾花类
class Iris:
    def __init__(self, sepal_length, sepal_width, petal_length, petal_width, species):
        self.sepal_length = sepal_length
        self.sepal_width = sepal_width
        self.petal_length = petal_length
        self.petal_width = petal_width
        self.species = species

    # 返回 feature
    def feature(self):
        return [self.sepal_length, self.sepal_width, self.petal_length, self.petal_width]

    # 返回 label
    def label(self):
        if self.species == 'Iris-setosa':
            return 0
        elif self.species == 'Iris-versicolor':
            return 1
        elif self.species == 'Iris-virginica':
            return 2


# 定义数据集
class IrisDataset(Dataset):
    def __init__(self, features, labels):
        self.features = features
        self.labels = labels

    # 返回数据集长度
    def __len__(self):
        return len(self.features)

    def __getitem__(self, index):
        item_feature = self.features[index]
        item_label = self.labels[index]
        return item_feature, item_label


class Model(torch.nn.Module):
    def __init__(self, n_feature, n_hidden, n_label):
        super(Model, self).__init__()
        self.hidden = torch.nn.Linear(n_feature, n_hidden)      # 定义隐藏层网络
        self.output = torch.nn.Linear(n_hidden, n_label)        # 定义输出层网络

    def forward(self, x):
        x = torch.nn.functional.relu(self.hidden(x))            # 隐藏层的激活函数,采用relu,也可以采用sigmod,tanh
        x = torch.nn.functional.softmax(self.output(x), dim=1)  # 输出层softmax
        return x


def get_data():
    tic = time.time()
    print('读取cvs文件')
    csv_path = '/Users/bakako/Downloads/archive/Iris.csv'
    datasource = pd.read_csv(csv_path)
    datasource_len = len(datasource)

    # 构建数据集矩阵 n 行，4 列

    # [[0. 0. 0. 0.]
    #       ...
    #  [0. 0. 0. 0.]]
    iris_features = np.zeros((datasource_len, 4), dtype=float)
    iris_labels = []

    print('构造数据结构')
    for i in range(datasource_len):
        # 创建 iris 对象
        iris = Iris(datasource.SepalLengthCm[i], datasource.SepalWidthCm[i],
                    datasource.PetalLengthCm[i], datasource.PetalWidthCm[i],
                    datasource.Species[i])

        # iris = Iris(datasource['SepalLengthCm'][i], datasource['SepalWidthCm'][i],
        #             datasource['PetalLengthCm'][i], datasource['PetalWidthCm'][i],
        #             datasource['Species'][i])

        # [[0. 0. 0. 0.]  <- 将元素一行一行进行替换
        #       ...
        #  [0. 0. 0. 0.]]
        iris_features[[i], :] = iris.feature()
        iris_labels.append(iris.label())

    # 把数据集按照 8:2 的比例来划分为训练集和测试集
    train_features, \
        test_features, \
        train_labels, \
        test_labels = train_test_split(iris_features, iris_labels, test_size=0.2)

    print('构造训练集')
    # 将训练集和测试集转换为PyTorch的张量对象并设置数据类型，加上to(device)可以运行在GPU上
    train_dataset = IrisDataset(torch.FloatTensor(train_features),
                                torch.LongTensor(train_labels))
    test_features = torch.FloatTensor(test_features)
    test_labels = torch.LongTensor(test_labels)

    toc = time.time()
    print('Loading Time: ' + str(1000 * (toc - tic)) + 'ms')
    print('')
    return train_dataset, test_features, test_labels


def train_model(dataset, epochs, gpu):
    tic = time.time()
    device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
    model = Model(n_feature=4, n_hidden=40, n_label=3)
    if gpu:
        model.to(device)

    optimizer = torch.optim.SGD(model.parameters(), lr=0.02)
    loss_func = torch.nn.CrossEntropyLoss()
    batch_size = 5  # 每次训练 5 个样本
    dataloader = DataLoader(dataset, batch_size=batch_size, shuffle=True, num_workers=0, pin_memory=True)

    # epoch 为训练次数
    for epoch in range(epochs):
        # 分 120/5 = 24 次训练
        for step, (features, labels) in enumerate(dataloader):
            if gpu:
                features = features.to(device)
                labels = labels.to(device)

            # 前向传播计算输出结果
            output = model(features)
            loss = loss_func(output, labels)
            # 注意 labels 的结构应该是 [0. 0. 0.]，而不是[[0.], [0.], [0.]]
            # loss 必须是[x, x, x] -> y，而不是[x, x, x] -> [y]
            # 否则会报错：0D or 1D target tensor expected, multi-target not supported
            # 如果出现这种情况，可以进行降维 labels = labels.squeeze(1)

            # 反向传播，更新梯度并优化模型参数
            optimizer.zero_grad()
            loss.backward()
            optimizer.step()
            print('Epoch: ', epoch + 1, '| Step: ', step + 1, '| Loss: ', loss.item())

    toc = time.time()
    print('训练结束')
    print('Train time:' + str(1000 * (toc - tic)) + 'ms')
    print('')

    return model


if __name__ == '__main__':
    gpu = False
    train_dataset, test_features, test_labels = get_data()
    model = train_model(train_dataset, 100, gpu)
    result = model(test_features)
    # prediction = torch.argmax(result, 1)  # 求最大值的索引
    prediction = torch.max(result, 1)[1]    # 这里第一个1表示求三个结果里的最大值，第二1表示我们只要索引(1返回index, 0返回原值)
    correct = (prediction == test_labels).sum().item()
    accuracy = float(correct) / float(len(test_features))
    print("莺尾花预测准确率:", accuracy)
```

