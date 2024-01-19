---
title: Swift 获取工程版本号
date: 2024-01-13 14:08:09
categories:
- [环境配置, Xocde]
tags:
- Swift
- iOS Development
math: false
---

关于版本号具体可看 [Xcode 工程设置构建版本号自动递增](https://kangzubin.com/xcode-buildversion-increase/)

我们这里取的是 `Verson` 而不是 `Build`，所以可以参考 [How do I get the App version and build number using Swift?](https://stackoverflow.com/questions/25965239/how-do-i-get-the-app-version-and-build-number-using-swift) 中的：

```swift
let appVersion = Bundle.main.infoDictionary?["CFBundleShortVersionString"] as? String
```

关于使用

```swift
Text(appVersion ?? "-.-.-")
```
