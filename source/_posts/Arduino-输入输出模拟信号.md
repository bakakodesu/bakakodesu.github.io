---
title: Arduino 输入输出模拟信号
date: 2023-08-10 16:10:31
categories:
- [Code, Arduino]
tags:
- Arduino
math: false
---

在 Arduino UNO 上，除了 14 个数字输入/输出引脚，还带有 6 个编号 A 的模拟输入引脚。Arduino 可以读取这些引脚上输入的模拟值，即读取引脚上输入的电压大小。模拟信号是用连续变化的物理量表示的信息，信号随时间作连续变化。

模拟输入引脚是带有 ADC（Analog-to-Digital Converter，模数转换器）功能的引脚。它可以将外部输入的模拟信号转换为芯片运算时可以识别的数字信号，从而实现读入模拟值的功能。

Arduino Uno模拟输入功能有 10 位精度，即可以将0～5V的电压信号转换为 0～1023 的整数形式表示。换句话说，我们可以将 5V 等分成 1024 份。0V 的输入信号对应着数值 0，而 5V 的输入信号对应着 1023。

### 输入信号

模拟输入功能需要使用 `analogRead()` 函数：

```c++
int value = analogRead(pin)
```

参数 pin 是指定要读取模拟值的引脚，被指定的引脚必须是模拟输入引脚。如 `analogRead(A0)`，即是读取A0引脚上的模拟值。Arduino 读取一次模拟输入需要消耗 100 微秒的时间，最大频率为 10000HZ。

**注意：在模拟输入引脚没有任何连接的情况下，用 `analogRead()` 读取该引脚，这时获得的返回值为不固定的数值。这个数值可能受到多种因素影响，如将手靠近引脚也可能使得该返回值产生变化。**

示例代码：

```c++
// 传感器数据传输引脚连接在Arduino的A0引脚
// 传感器另外两个引脚分别连接在Arduino的5V和GND引脚

// 变量sensorValue用来存储模拟输入信号
int sensorValue = 0;           

void setup()
{
  // Arduino串口通讯初始化 
  Serial.begin(9600); 
}

void loop()
{
  // 读取引脚A0输入信号
  sensorValue = analogRead(A0);   

  // 将A0输入信号转换为0-1023之间的数值
  // 并且通过串口监视器显示 
  Serial.println(sensorValue); 
}
```

### 输出信号

与模拟输入功能对应的是模拟输出功能，使用 `analogWrite()` 函数实现这个功能。但该函数并不是输出真正意义上的模拟值，而是以一种特殊的方式来达到输出近似模拟值的效果，这种方式叫做脉冲宽度调制（PWM，Pulse Width Modulation）。

在Arduino Uno中，提供PWM功能的引脚为3、5、6、9、10、11。

当使用 `analogWrite()` 函数时，指定引脚会通过高低电平的不断转换输出一个周期固定的方波，通过改变高低电平在每个周期中所占的比例（占空比），而得到近似输出不同的电压的效果。

需要注意的是，这里仅仅是得到了近似模拟值输出的效果，如果要输出真正的模拟值，还需要加上外围滤波电路。

```c++
analogWrite(pin, value)
```

参数 pin 是指定要输出 PWM 波的引脚，参数 value 指定是 PWM 的脉冲宽度，范围为 0～255。

在 `analogWrite()` 和 `analogRead()` 函数内部，已经完成了引脚的初始化，因此不用在 `setup()` 函数中进行初始化操作（数字信号的 `pinMode()`）。
