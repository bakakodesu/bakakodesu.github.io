---
title: Arduino 输入输出数字信号
date: 2023-08-11 16:13:23
categories:
- [Code, Arduino]
tags:
- Arduino
math: false
---

Arduino Uno 上每一个带有数字编号的引脚，都是数字引脚，包括写有 A 编号的模拟输入引脚。

数字信号是以 0、1 表示的电平不连续变化的信号，也就是以二进制的形式表示的信号。在 Arduino 中数字信号通过高低电平来表示，高电平则为数字信号 1，低电平则为数字信号 0。

在使用输入或输出功能前，需要先通过 `pinMode()` 函数配置引脚的模式为输入模式或输出模式：

```c++
pinMode(pin, mode);
```

参数 pin 为指定配置的引脚编号；参数 mode 为指定的配置模式，包括：

- INPUT，输入模式
- OUTPUT，输出模式
- INPUT_PULLUP，输入上拉模式

### 输出模式

配置成输出模式后，还需要使用 `digitalWrite()` 让其输出高电平或者是低电平。其调用形式为：

```c++
digitalWrite(pin, value);
```

参数 pin 为指定输出的引脚编号；参数 value 为要指定输出的电平，使用 HIGH 指定输出高电平，或是使用 LOW 指定输出低电平。Arduino 中输出的低电平为 0V，输出的高电平为当前 Arduino 的工作电压。例如 Arduino UNO 的工作电压为 3.3V，其高电平输出也是 3.3V；Arduino Uno 工作电压为 5V，所以高电平输出也就是 5V。

示例代码：

```c++
/*
Blink
等待一秒钟，点亮LED，再等待一秒钟，熄灭LED，如此循环
*/ 

#define ledPin A0     

void setup(){                	
  pinMode(ledPin, OUTPUT);      // 初始化A0引脚设置为输出状态
}

void loop() {
  digitalWrite(ledPin, HIGH);
  delay(1000);
  digitalWrite(ledPin, LOW);
  delay(1000);
}
```

### 输入模式

数字引脚除了用于输出信号外，还可以用 `digitalRead()` 函数读取外部输入的数字信号，其调用形式为：

```c++
int value = digitalRead(pin);
```

参数 pin 为指定读取状态的引脚编号；返回值 value 为获取到的信号状态，1 为高电平，0 为低电平。

Arduino Uno 会将大于 1.5V 的输入电压视为高电平，小于 1.3V 的电压视为低电平。所以，即使输入电压不太准确，Arduino Uno 也可以正常识别。
