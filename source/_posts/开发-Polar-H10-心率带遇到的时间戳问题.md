---
title: 开发 Polar H10 心率带遇到的时间戳问题
date: 2024-01-18 11:29:15
categories:
- 随笔
tags:
- 心率
- 传感器
- Swift
math: false
---

可参考的内容包括：

1. [How to check currently-set epoch for timestamp](https://github.com/polarofficial/polar-ble-sdk/issues/192)
2. [setLocalTime sets incorrect time and date](https://github.com/polarofficial/polar-ble-sdk/issues/165)
3. [What is the zero date for the H10 timestamp?](https://github.com/polarofficial/polar-ble-sdk/issues/221)

### 时间戳的基准问题

首先需要明确的一点是，Polar H10 的基准时间并不是 1970-01-01 00:00:00（GMT）。而是 2000-01-01 00:00:00（GMT）。

这意味着 Polar H10 产生的时间戳，要比标准时间戳少了 946684800000 毫秒。

> you may use the epoch time to human readable time converters in your code, which are usually based on the unix epoch of Jan 01 1970 00:00:00. Then you shall add the offset between Jan 01 1970 00:00:00 and Jan 01 2000 00:00:00 to timestamp before passing the timestamp to converter. Offset in nanoseconds is 946684800000000000

也就是说，处理后续时间戳时，需要加上 1970-01-01 到 2000-01-01 这 30 年的 946684800000 毫秒。

### 传感器重启问题

Polar H10 在非佩戴时间会自动关机，也就是断电。等到重启后，传感器的时间芯片会默认从 2019-01-01 00:00:00 (GMT) 开始计时。

*纳秒为 599616000000000000 ns*

> the time of the sensor is set to default value when sensor resets or battery runs out. For the Polar H10 (v.3.1.1) the default time is Jan 01 2019 00:00:00 (GMT), which in nanosecond format is 599616000000000000

所以可以看到 *599616067917010444* 这样的时间戳。

```
举个例子
我在 2024-01-18 11:29:15 启动传感器并打印时间，得到的时间为 2019-01-01 00:00:00
```

这样显然是不可以使用的，所以需要设置传感器的时间与现在同步。即：

```swift
Task {
	await bleSdkManager.setTime()
}
```

### 设置传感器时间问题

SDK 自带的 setTime() API 存在一个问题——

**它会将本地时间设置成 GMT 时间**

```
按理说，如果本地（北京 UTC+8）时间为 15:00，那么 GMT 的时间应该为 07:00。
当设置好传感器时间后，传感器打印出来的时间戳对应的 GMT 时间也应该是 07:00 才对。
```

但现在的问题是

```
我在 15:00（北京 UTC+8）设置传感器的时间并打印时间戳。
传感器打印出来的时间戳对应的时间是 15:00（GMT）或者说 23:00（北京 UTC+8）。
而不是 07:00（GMT）或者说 15:00（北京 UTC+8）。
也就是说，在 setTime() 后传感器的时间要快了 8 小时。
```

### 传感器时间戳处理的具体流程

1. 每次佩戴 Polar H10 后，需要通过 setTime() 初始化传感器时间，让传感器不再以 2019-01-01 00:00:00 (GMT) 起始开始计时；
2. 在获得时间戳后，需要先加上 *946684800000000000 ns*，以补充 1970-01-01 00:00:00（GMT）到 2000-01-01 00:00:00（GMT）这段时间；
3. 之后，再**减去** *28800000000000 ns* 以修正 setTime() 函数将本地时间设置成 GMT 时间所造成的误差。具体数值视 UTC 时区而定，这里用的是北京东八区，也就是 UTC+8，所以是减去 *8x60x60 s*。
