---
title: Swift String format 的相关使用方法
date: 2024-01-19 15:01:51
categories:
- [Code, Swift]
tags:
- Swift
math: false
---

### 最简单的方法

字符串中包裹着 `\()`

```swift
let age = 10
let name = "xxx"

let string = String(format: "%@ Age Is %d", name, age)

Text("\(name) Age Is \(age)")
Text(name + "Age Is" + String(age))
Text(string)
```

### 整数与小数

#### 整数

```swift
let string = String(format: "%d", 123892674027)
Text(string)
// 结果却是："-661377557"
let string = String(format: "%ld", 123892674027)
```

如果数据大，可能大于 2147483647（10 位），使用 `"%ld"`，否则使用 `"%d"` 即可。

长度修饰符

* h，代表 shor t或者 unsigned short
* l，代表 long 或者 unsiged long  (ll 表示 long long 或者 unsigned long long) Int
* L，代表 Long double

#### 小数

```swift
let i = 1.1111
let string = String(format: "%f", i)	// 1.1111，默认保留 6 位小数
let string = String(format: "%.2f", i)	// 1.11，输入几保留几位小数
```

### 占位符

```swift
let minutes: Int = 8
let seconds: Int = 9
String(format: "%02d:%02d", minutes, seconds) //08:09


let i: Int = 36
let i2: Int = 350087
// 固定四位显示
String(format: "%4d", i) // "  36" （注意有两个空格）
String(format: "%4d", i2) // "350087" (位数大于，则全部显示)
 
// 固定四位显示，不足补0
String(format: "%04d", i) // "0036"
String(format: "%04d", i2) // "350087" (位数大于，则全部显示)
```

参考

1. [Swift 数字字符串格式化](https://blog.csdn.net/qq_14920635/article/details/96479691)
2. [透過 String 讓數值格式化呈現(整數補零、小數四捨五入至第幾位數)](https://medium.com/彼得潘的-swift-ios-app-開發教室/swift-透過-string-讓數值格式化呈現-整數補零-小數四捨五入至第幾位數-eefb15b998b6)
