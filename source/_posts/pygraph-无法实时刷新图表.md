---
title: pygraph 无法实时刷新图表
date: 2023-10-06 00:10:47
categories:
- [Code, Python]
tags:
- Python
math: false
---

```python
import numpy as np
import serial
from PyQt5.QtWidgets import QMainWindow, QApplication, QVBoxLayout, QWidget
from PyQt5.QtCore import QThread, QTimer
import pyqtgraph as pg
import sys


class Window(QMainWindow):
    def __init__(self, port, baud):
        super(Window, self).__init__()
        self.roll_plot = pg.PlotWidget()
        self.ser = serial.Serial(port, baud, timeout=1)

        self.roll_plot.setLabel('left', 'Flow L/min')
        self.roll_plot.setLabel('bottom', 'Time s')
        self.roll_curve = self.roll_plot.plot(pen='r')

        win_size = 50  # 5s
        self.roll_x = np.zeros(win_size)
        self.roll_y = np.zeros(win_size)

        layout = QVBoxLayout()
        layout.addWidget(self.roll_plot)
        
        self.central_widget = QWidget()
        self.central_widget.setLayout(layout)
        self.setCentralWidget(self.central_widget)

        self.thread = Sensor(self.ser, self.roll_x, self.roll_y, self.roll_curve)
        self.thread.start()


class Sensor(QThread):
    def __init__(self, ser, roll_x, roll_y, roll_curve):
        super(Sensor, self).__init__()
        self.ser = ser
        self.roll_x = roll_x
        self.roll_y = roll_y
        self.roll_curve = roll_curve

    def run(self):
        while True:
            self.update_flow()

    def update_flow(self):
        try:
            sensor_value = self.ser.readline()
            value = float(sensor_value)
        except Exception as e:
            value = 0.0
            print(e)

        self.roll_x[:-1] = self.roll_x[1:]
        self.roll_y[:-1] = self.roll_y[1:]

        self.roll_x[-1] = self.roll_x[-2] + 0.1
        self.roll_y[-1] = value

        # 这里的做法是在 Sensor 线程中调用 setData 刷新图表
        # 但问题是在程序运行一段时间后，图表不会自动刷新
        self.roll_curve.setData(self.roll_x, self.roll_y)
        

def main():
    app = QApplication(sys.argv)
    flow_win = Window('/dev/tty.usbmodem1301', 115200)
    flow_win.show()
    sys.exit(app.exec_())


if __name__ == '__main__':
    main()
```

图表不会自动刷新的原因是 setData 必须在主线程中运行！

修改为 **Sensor 线程用于采集数据，主线程用来刷新图表**:

```python
import numpy as np
import serial
from PyQt5.QtWidgets import QMainWindow, QApplication, QVBoxLayout, QWidget
from PyQt5.QtCore import QThread, QTimer
import pyqtgraph as pg
import sys


class Window(QMainWindow):
    def __init__(self, port, baud):
        super(Window, self).__init__()
        self.roll_plot = pg.PlotWidget()
        self.ser = serial.Serial(port, baud, timeout=1)

        self.roll_plot.setLabel('left', 'Flow L/min')
        self.roll_plot.setLabel('bottom', 'Time s')
        self.roll_curve = self.roll_plot.plot(pen='r')

        win_size = 50  # 5s
        self.roll_x = np.zeros(win_size)
        self.roll_y = np.zeros(win_size)

        layout = QVBoxLayout()
        layout.addWidget(self.roll_plot)

        self.central_widget = QWidget()
        self.central_widget.setLayout(layout)
        self.setCentralWidget(self.central_widget)

        # 仅采集数据
        self.thread = Sensor(self.ser, self.roll_x, self.roll_y)
        self.thread.start()

        # 100ms 刷新一次图表
        self.timer = QTimer()
        self.timer.timeout.connect(lambda: self.plot_data())
        self.timer.start(100)

    def plot_data(self):
        self.roll_curve.setData(self.roll_x, self.roll_y)


class Sensor(QThread):
    def __init__(self, ser, roll_x, roll_y):
        super(Sensor, self).__init__()
        self.ser = ser
        self.roll_x = roll_x
        self.roll_y = roll_y

    def run(self):
        while True:
            self.update_flow()

    def update_flow(self):
        try:
            sensor_value = self.ser.readline()
            value = float(sensor_value)
        except Exception as e:
            value = 0.0
            print(e)

        self.roll_x[:-1] = self.roll_x[1:]
        self.roll_y[:-1] = self.roll_y[1:]

        self.roll_x[-1] = self.roll_x[-2] + 0.1
        self.roll_y[-1] = value


def main():
    app = QApplication(sys.argv)
    flow_win = Window('/dev/tty.usbmodem1301', 115200)
    flow_win.show()
    sys.exit(app.exec_())


if __name__ == '__main__':
    main()
```

