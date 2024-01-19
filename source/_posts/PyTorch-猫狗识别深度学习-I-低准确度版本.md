---
title: PyTorch 猫狗识别深度学习 I 低准确度版本
date: 2023-08-07 16:06:58
categories:
- [Code, Python]
tags:
- Python
- 深度学习
- 计算机视觉
math: false
---

### 数据源

[Cat and Dog](https://www.kaggle.com/datasets/tongpython/cat-and-dog)

下载的文件层级为：

PetImages(doc) - Cat(doc)/Dog(doc) - image(jpg)

需要注意狗的 11702 和猫的 666 这两张图片损坏了。在加载数据集的时候会出现警告。

### 处理图片

#### 获取猫狗图片的 txt

```python
def get_txt(path, file_name, label):
    # path：dataset的根目录
    # file_path：dataset文件夹
    file_path = os.path.join(path, file_name)
    data = os.listdir(file_path)
    # 'w' is mean write
    f = open(path + '/' + file_name + 'data.txt', 'w')
    for item in data:
        f.write(item + ' ' + str(label) + ' ' + file_name + '\n')
    f.close()
```

这里是将 PetImages 文件夹下的 Cat 或者 Dog 文件打开，遍历里面的照片，最后输出 txt 文件。

txt 的内容为（以猫为例）：

```
1.jpg 0 Cat
2.jpg 0 Cat
3.jpg 0 Cat
4.jpg 0 Cat
```

这里，Cat 的 label 为 0，Dog 的 label 为 1。

#### 混合猫狗图片的 txt

```python
def mix_txt(path, file1_name, file2_name):
    f = open(path + '/' + 'DataSet.txt', 'w')
    # 'r' is mean read
    f1 = open(path + '/' + file1_name + 'Data.txt', 'r')
    # readline/readlines
    data1 = f1.readlines()
    # PC 上生成的 txt 有 DStore 信息。合并 txt 时需要只读取第一行之后的内容
    # data1 = data1[1:]
    for item in data1:
        f.write(item)

    f2 = open(path + '/' + file2_name + 'Data.txt', 'r')
    data2 = f2.readlines()
    # data2 = data2[1:]
    for item in data2:
        f.write(item)

    f.close()
    f1.close()
    f2.close()
```

### 构建数据集

#### 定义数据集类，通过 txt 索引将所有图片变成数据集

```python
class ImageDataset(Dataset):
    def __init__(self, path, transform):
        self.path = path
        self.transform = transform
        self.txt_path = self.path + '/' + 'Dataset.txt'
        f = open(self.txt_path, 'r')
        data = f.readlines()
        images = []
        labels = []

        for item in data:
            # 去掉空格，并组成数组
            word = item.split()
            images.append(os.path.join(self.path, word[2], word[0]))
            labels.append(int(word[1]))

        # 张量化
        labels = torch.LongTensor(labels)
        self.images = images
        self.labels = labels

    def __getitem__(self, index):
        image = self.images[index]
        label = self.labels[index]
        image = Image.open(image).convert('RGB')
        image = self.transform(image)
        return image, label

    def __len__(self):
        return len(self.labels)
```

#### 初始化数据集，并切割

```python
def get_dataset(path, transform):
    tic = time.time()
    print('Loading Dataset...')
    dataset = ImageDataset(path, transform)
    train, test = train_test_split(dataset, test_size=0.2)
    toc = time.time()
    print('Loading Over, Cost Time: ' + str(toc - tic) + 's')
    return train, test
```

### MODEL

#### 定义模型

不负责的定义

```python
class Model(nn.Module):
    # ValueError:optimizer got an empty parameter list
    # init写错（int），没有__，没有super都会报错
    def __init__(self):
        super(Model, self).__init__()
        # 卷积层
        # original 3 x 256 x 256
        # conv1 -> 8 x 256 x 256
        self.conv1 = nn.Conv2d(in_channels=3, out_channels=8, kernel_size=3, stride=1, padding=1)
        # pool1 -> 8 x 128 x 128
        self.pool1 = nn.MaxPool2d(2, 2)

        # conv2 -> 16 x 128 x 128
        self.conv2 = nn.Conv2d(in_channels=8, out_channels=16, kernel_size=3, stride=1, padding=1)
        # pool2 -> 16 x 64 x 64
        self.pool2 = nn.MaxPool2d(2, 2)

        # conv3 -> 32 x 64 x 64
        self.conv3 = nn.Conv2d(in_channels=16, out_channels=32, kernel_size=3, stride=1, padding=1)
        # pool3 -> 32 x 32 x 32
        self.pool3 = nn.MaxPool2d(2, 2)

        # 全连接层
        self.fc1 = nn.Linear(32 * 32 * 32, 256)
        self.fc2 = nn.Linear(256, 64)
        self.fc3 = nn.Linear(64, 2)

    def forward(self, x):
        x = self.conv1(x)
        x = nn.functional.relu(x)
        x = self.pool1(x)

        x = self.conv2(x)
        x = nn.functional.relu(x)
        x = self.pool2(x)

        x = self.conv3(x)
        x = nn.functional.relu(x)
        x = self.pool3(x)

        # x = x.view(32 * 32 * 32, -1)
        # mat1 and mat2 shapes cannot be multiplied (32768x50 and 32768x256)
        # batch_size = 50
        # 要改成 50x32768 and 32768x256
        x = x.view(-1, 32 * 32 * 32)
        x = self.fc1(x)
        x = nn.functional.relu(x)

        x = self.fc2(x)
        x = nn.functional.relu(x)

        x = self.fc3(x)
        x = nn.functional.softmax(x, dim=1)

        return x
```

注意两个点：

1. init 写错（int），没有__，没有 super 都会报错 ValueError:optimizer got an empty parameter list；
2. x = x.view(32 * 32 * 32, -1) 会报错 mat1 and mat2 shapes cannot be multiplied (32768x50 and 32768x256)，要行对列。

#### 训练模型

```python
def get_model(train, test, batch_size, gpu, lr, epochs):
    device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
    model = Model()
    # SGD
    optimizer = torch.optim.Adam(model.parameters(), lr=lr)
    # RuntimeError: Boolean value of Tensor with more than one value is ambiguous
    # 初始化的时候，后面加个()
    loss_func = nn.CrossEntropyLoss()

    if gpu:
        # Input type (torch.FloatTensor) and weight type (torch.cuda.FloatTensor) should be the same
        # 要model = model.to(device)，不能写model.to(device)
        model = model.to(device)
        loss_func = loss_func.to(device)

    train_dataloader = DataLoader(train,
                                  batch_size=batch_size,
                                  shuffle=True,
                                  num_workers=0,
                                  pin_memory=True)

    test_dataloader = DataLoader(test,
                                 batch_size=batch_size,
                                 shuffle=True,
                                 num_workers=0,
                                 pin_memory=True)

    for epoch in range(epochs):
        tic = time.time()
        print('Training')
        for step, (train_images, train_labels) in enumerate(train_dataloader):

            if gpu:
                train_images = train_images.to(device)
                train_labels = train_labels.to(device)

            prediction = model(train_images)
            loss = loss_func(prediction, train_labels)
            optimizer.zero_grad()
            loss.backward()
            optimizer.step()
            # print('Epoch: ', epoch + 1, '| Step: ', step + 1, '| Loss: ', loss.item())

        toc = time.time()
        print('Train', epoch + 1, 'Over')
        print('Train time:' + str(toc - tic) + 's')
        print('Test Dataset Loading...')

        correct = 0
        for step, (test_images, test_labels) in enumerate(test_dataloader):

            if gpu:
                test_images = test_images.to(device)
                test_labels = test_labels.to(device)

            result = model(test_images)
            prediction = torch.argmax(result, 1)
            correct += (prediction == test_labels).sum().item()
        accuracy = correct / len(test_dataset)
        print('Test Dataset:', len(test_dataset), '| Correct:', correct, '| Accuracy:', accuracy)
        print('')
        time.sleep(3)

    return model
```

### 整体代码

```python
import time
import os
import torch
import torch.nn as nn
from torch.utils.data import Dataset, DataLoader
from torchvision import transforms
from sklearn.model_selection import train_test_split
from PIL import Image


class ImageDataset(Dataset):
    def __init__(self, path, transform):
        self.path = path
        self.transform = transform
        self.txt_path = self.path + '/' + 'Dataset.txt'
        f = open(self.txt_path, 'r')
        data = f.readlines()
        images = []
        labels = []

        for item in data:
            # 去掉空格，并组成数组
            word = item.split()
            images.append(os.path.join(self.path, word[2], word[0]))
            labels.append(int(word[1]))

        # 张量化
        labels = torch.LongTensor(labels)
        self.images = images
        self.labels = labels

    def __getitem__(self, index):
        image = self.images[index]
        label = self.labels[index]
        image = Image.open(image).convert('RGB')
        image = self.transform(image)
        return image, label

    def __len__(self):
        return len(self.labels)


class Model(nn.Module):
    # ValueError:optimizer got an empty parameter list
    # init写错（int），没有__，没有super都会报错
    def __init__(self):
        super(Model, self).__init__()
        # 卷积层
        # original 3 x 256 x 256
        # conv1 -> 8 x 256 x 256
        self.conv1 = nn.Conv2d(in_channels=3, out_channels=8, kernel_size=3, stride=1, padding=1)
        # pool1 -> 8 x 128 x 128
        self.pool1 = nn.MaxPool2d(2, 2)

        # conv2 -> 16 x 128 x 128
        self.conv2 = nn.Conv2d(in_channels=8, out_channels=16, kernel_size=3, stride=1, padding=1)
        # pool2 -> 16 x 64 x 64
        self.pool2 = nn.MaxPool2d(2, 2)

        # conv3 -> 32 x 64 x 64
        self.conv3 = nn.Conv2d(in_channels=16, out_channels=32, kernel_size=3, stride=1, padding=1)
        # pool3 -> 32 x 32 x 32
        self.pool3 = nn.MaxPool2d(2, 2)

        # 全连接层
        self.fc1 = nn.Linear(32 * 32 * 32, 256)
        self.fc2 = nn.Linear(256, 64)
        self.fc3 = nn.Linear(64, 2)

    def forward(self, x):
        x = self.conv1(x)
        x = nn.functional.relu(x)
        x = self.pool1(x)

        x = self.conv2(x)
        x = nn.functional.relu(x)
        x = self.pool2(x)

        x = self.conv3(x)
        x = nn.functional.relu(x)
        x = self.pool3(x)

        # x = x.view(32 * 32 * 32, -1)
        # mat1 and mat2 shapes cannot be multiplied (32768x50 and 32768x256)
        # batch_size = 50
        # 要改成 50x32768 and 32768x256
        x = x.view(-1, 32 * 32 * 32)
        x = self.fc1(x)
        x = nn.functional.relu(x)

        x = self.fc2(x)
        x = nn.functional.relu(x)

        x = self.fc3(x)
        x = nn.functional.softmax(x, dim=1)

        return x


def get_txt(path, file_name, label):
    # path：dataset的根目录
    # file_path：dataset文件夹
    file_path = os.path.join(path, file_name)
    data = os.listdir(file_path)
    # 'w' is mean write
    f = open(path + '/' + file_name + 'Data.txt', 'w')
    for item in data:
        f.write(item + ' ' + str(label) + ' ' + file_name + '\n')
    f.close()


def mix_txt(path, file1_name, file2_name):
    f = open(path + '/' + 'DataSet.txt', 'w')
    # 'r' is mean read
    f1 = open(path + '/' + file1_name + 'Data.txt', 'r')
    # readline/readlines
    data1 = f1.readlines()
    # PC 上生成的 txt 有 DStore 信息。合并 txt 时需要只读取第一行之后的内容
    # data1 = data1[1:]
    for item in data1:
        f.write(item)

    f2 = open(path + '/' + file2_name + 'Data.txt', 'r')
    data2 = f2.readlines()
    # data2 = data2[1:]
    for item in data2:
        f.write(item)

    f.close()
    f1.close()
    f2.close()


def get_dataset(path, transform):
    tic = time.time()
    print('Loading Dataset...')
    dataset = ImageDataset(path, transform)
    train, test = train_test_split(dataset, test_size=0.2)
    toc = time.time()
    print('Loading Over, Cost Time: ' + str(toc - tic) + 's')
    return train, test


def get_model(train, test, batch_size, gpu, lr, epochs):
    device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
    model = Model()
    # SGD
    optimizer = torch.optim.Adam(model.parameters(), lr=lr)
    # RuntimeError: Boolean value of Tensor with more than one value is ambiguous
    # 初始化的时候，后面加个()
    loss_func = nn.CrossEntropyLoss()

    if gpu:
        # Input type (torch.FloatTensor) and weight type (torch.cuda.FloatTensor) should be the same
        # 要model = model.to(device)，不能写model.to(device)
        model = model.to(device)
        loss_func = loss_func.to(device)

    train_dataloader = DataLoader(train,
                                  batch_size=batch_size,
                                  shuffle=True,
                                  num_workers=0,
                                  pin_memory=True)

    test_dataloader = DataLoader(test,
                                 batch_size=batch_size,
                                 shuffle=True,
                                 num_workers=0,
                                 pin_memory=True)

    for epoch in range(epochs):
        tic = time.time()
        print('Training')
        for step, (train_images, train_labels) in enumerate(train_dataloader):

            if gpu:
                train_images = train_images.to(device)
                train_labels = train_labels.to(device)

            prediction = model(train_images)
            loss = loss_func(prediction, train_labels)
            optimizer.zero_grad()
            loss.backward()
            optimizer.step()
            # print('Epoch: ', epoch + 1, '| Step: ', step + 1, '| Loss: ', loss.item())

        toc = time.time()
        print('Train', epoch + 1, 'Over')
        print('Train time:' + str(toc - tic) + 's')
        print('Test Dataset Loading...')

        correct = 0
        for step, (test_images, test_labels) in enumerate(test_dataloader):

            if gpu:
                test_images = test_images.to(device)
                test_labels = test_labels.to(device)

            result = model(test_images)
            prediction = torch.argmax(result, 1)
            correct += (prediction == test_labels).sum().item()
        accuracy = correct / len(test_dataset)
        print('Test Dataset:', len(test_dataset), '| Correct:', correct, '| Accuracy:', accuracy)
        print('')
        time.sleep(3)

    return model


if __name__ == '__main__':
    # root_path = 'E:/Mac/PetImages'
    root_path = '/Users/bakako/Downloads/PetImages'
    get_txt(root_path, file_name='Cat', label=0)
    get_txt(root_path, file_name='Dog', label=1)
    mix_txt(root_path, file1_name='Cat', file2_name='Dog')
    custom_transform = transforms.Compose([
        transforms.Resize(288),
        transforms.CenterCrop(256),
        transforms.ToTensor()
    ])

    # batch_size
    # https://blog.51cto.com/u_15127688/4678771
    # 这相比于全数据集训练，相当于是在寻找最优时人为增加了一些随机噪声，来修正由局部数据得到的梯度，尽量避免因 batch size 过大陷入局部最优
    # 吃内存

    train_dataset, test_dataset = get_dataset(root_path, custom_transform)
    cat_vs_dog_model = get_model(train=train_dataset, test=test_dataset,
                                 batch_size=25,
                                 gpu=False,
                                 lr=0.0001,
                                 epochs=30)


```
