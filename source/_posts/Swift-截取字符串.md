---
title: Swift 截取字符串
date: 2024-01-19 13:59:22
categories:
- [Code, Swift]
tags:
- Swift
math: false
---

```swift
// 头部截取 
let str = "asdfghjkl;'" 
let str1 = str.prefix(2); 
print(str1) 
// 结果: as 
   
// 尾部截取   
let str2 = str.suffix(3); 
print(str2) 
// 结果: l;' 
```
