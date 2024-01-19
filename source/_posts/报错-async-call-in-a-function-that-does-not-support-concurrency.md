---
title: 报错 'async' call in a function that does not support concurrency
date: 2024-01-18 14:36:14
categories:
- 随笔
tags:
- Swift
math: false
---

异步函数的调用方式是

```swift
Task {
	await yourFunction()
}
```

