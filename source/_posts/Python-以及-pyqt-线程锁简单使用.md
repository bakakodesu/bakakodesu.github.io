---
title: Python 以及 pyqt 线程锁简单使用
date: 2023-08-29 17:16:06
categories:
- [Code, Python]
tags:
- Python
math: false
---

### 采用 threading 并加锁

```python
import threading


# 重写线程
class Calculate(threading.Thread):
    def __init__(self, name, count):
        threading.Thread.__init__(self)
        self.name = name
        self.count = count

    def run(self):
        # 每0.1秒执行一次cal函数，不要带()
        timer = threading.Timer(0.1, self.cal)
        timer.start()

    def cal(self):
        print(self.name + ' is ' + self.count)
        self.count += 1


if __name__ == '__main__':
    count1 = 0
    count2 = 0
    # 初始化线程
    calculate1 = Calculate('COUNT1', count1)
    calculate2 = Calculate('COUNT2', count2)
    calculate1.start()
    calculate2.start()

# ouput:
# COUNT1 is 0
# COUNT1 is 1
# COUNT1 is 2
# COUNT2 is 0
# COUNT1 is 3
# COUNT2 is 1
# COUNT1 is 4
# COUNT2 is 2
# COUNT2 is 3

```

可以发现两个线程之间的执行并不同步，若想同步执行，则需要加「线程锁」。

```python
import threading


# 重写线程
class Calculate(threading.Thread):
    def __init__(self, name, count, acquire, release):
        threading.Thread.__init__(self)
        self.name = name
        self.count = count
        self.acquire = acquire  # 需要加锁线程
        self.release = release  # 需要解锁线程

    def run(self):
        timer = threading.Timer(0.1, self.cal)
        timer.start()

    def cal(self):
        self.acquire.acquire()
        print(self.name + ' is ' + self.count)
        self.count += 1
        self.release.release()


if __name__ == '__main__':
    count1 = 0
    count2 = 0
    # 初始化线程锁
    count1_lock = threading.Lock()
    count2_lock = threading.Lock()
    # count2加锁，这样只能先运行没有加锁的count1
    count2_lock.acquire()
    calculate1 = Calculate('COUNT1', count1, count1_lock, count2_lock)
    calculate2 = Calculate('COUNT2', count2, count2_lock, count1_lock)

    calculate1.start()
    calculate2.start()

# ouput:
# COUNT1 is 0
# COUNT2 is 0
# COUNT1 is 1
# COUNT2 is 1
# COUNT1 is 2
# COUNT2 is 2
# COUNT1 is 3
# COUNT2 is 3
```

### 采用 QThread 并加锁

```python
import sys
from PyQt5.QtCore import QThread, QTimer, QMutex
from PyQt5.QtWidgets import QMainWindow, QApplication


# 重写线程
class Calculate(QThread):
    def __init__(self, name, count, lock, unlock):
        super(Calculate, self).__init__()
        self.name = name
        self.count = count
        self.lock = lock  # 需要加锁线程
        self.unlock = unlock  # 需要解锁线程

    def run(self):
        timer = QTimer()
        timer.timeout.connect(lambda: self.cal())
        timer.start(100)

    def cal(self):
        self.lock.lock()
        print(self.name + ' is ' + self.count)
        self.count += 1
        self.unlock.unlock()


# QThread只能在QT程序中运行
class Window(QMainWindow):
    def __init__(self):
        super(Window, self).__init__()
        self.count1 = 0
        self.count2 = 0
        # 初始化线程锁
        self.count1_lock = QMutex()
        self.count2_lock = QMutex()
        # count2加锁，这样只能先运行没有加锁的count1
        self.count2_lock.lock()
        self.calculate1 = Calculate('COUNT1', self.count1, self.count1_lock, self.count2_lock)
        self.calculate2 = Calculate('COUNT2', self.count2, self.count2_lock, self.count1_lock)

        self.calculate1.start()
        self.calculate2.start()


if __name__ == '__main__':
    app = QApplication(sys.argv)
    win = Window()
    win.show()
    sys.exit(app.exec_())

# ouput:
# COUNT1 is 0
# COUNT2 is 0
# COUNT1 is 1
# COUNT2 is 1
# COUNT1 is 2
# COUNT2 is 2
# COUNT1 is 3
# COUNT2 is 3
```

