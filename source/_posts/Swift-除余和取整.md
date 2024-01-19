---
title: Swift 除余和取整
date: 2023-08-31 23:46:43
categories:
- [Code, Swift]
tags:
- Swift
math: false
---

```swift
let x = 8.625
print(x / 0.75)
// Prints "11.5"

// 取整
let q = (x / 0.75).rounded(.towardZero)
// q == 11.0

// 除余
let r = x.truncatingRemainder(dividingBy: 0.75)
// r == 0.375

let x1 = 0.75 * q + r
// x1 == 8.625
```

