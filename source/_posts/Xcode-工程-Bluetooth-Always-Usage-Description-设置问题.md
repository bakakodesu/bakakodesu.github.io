---
title: Xcode 工程 Bluetooth Always Usage Description 设置问题
date: 2024-01-10 23:47:18
categories:
- [环境配置, Xocde]
tags:
- iOS Development
math: false
---

在进行蓝牙开发时，如果没有设置 Bluetooth Always Usage Description，则会提示：

```markdown
This app has crashed because it attempted to access privacy-sensitive data without a usage description. The app's Info.plist must contain an NSBluetoothAlwaysUsageDescription key with a string value explaining to the user how the app uses this data.
```

**类似于请求网络、请求定位一样。**

但是在 Xcode 13 后，传统的 info.plist 文件已经消失了。

苹果在[《Xcode13 Release Notes》](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.apple.com%2Fdocumentation%2Fxcode-release-notes%2Fxcode-13-release-notes)中写道：

> “Projects created from several templates no longer require configuration files such as entitlements and **Info.plist** files. Configure common fields in the **target’s Info tab, and build settings** in the project editor. These files are added to the project when additional fields are used. (68254857)”

**意思是说，从 Xcode13 开始，新建的工程不再要求使用配置文件（Info.plist、entitlements）。如果需要修改 Xcode 的配置，请直接在 Xcode 面板「target - Info - Custom iOS Target Properties 和 build settings」中设置**。

其实，早在 Xcode13 之前，Custom iOS Target Properties 这个面板就有了，只是 Xcode 会自动同步 Cusutom iOS Target Properties 和 Info.plist 文件。而现在，Xcode13 默认删除了 Info.plist 文件中的大部分属性，保留在 Cusutom iOS Target Properties 中。

具体的做法：

找到 TARGETS，选中 Build Settings，「All」与「Combined」，找到 info.plist Values，其中有一个选项是 **Privacy - Bluetooth Always Usage Description**，找到并填写内容，例如「需要打开的蓝牙」。

设置完成后，在第一次运行 App 时，程序会向用户请求蓝牙使用权限，请求操作显示的内容便是「需要打开的蓝牙」。
