---
title: Swift 汉字转码
date: 2023-10-10 10:04:16
categories:
- [Code, Swift]
tags:
- Swift
math: false
---

### 汉字转码

```swift
let URL = "https://www.baidu.com/s?wd=点个赞吧"
let result = str.addingPercentEncoding(withAllowedCharacters: .urlQueryAllowed)
print(result)
```

### 解码

```swift
let URL = result?.removingPercentEncoding
print(URL)
```
