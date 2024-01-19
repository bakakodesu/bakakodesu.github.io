---
title: Swift 导出 .csv 文件
date: 2023-09-13 15:35:12
categories:
- [Code, Swift]
tags:
- Swift
math: false
---

### 导出文件

数据格式如下：

```swift
struct Dot {
    
    public var number = Int()
    public var gravityX = Double()
    public var gravityY = Double()
    public var gravityZ = Double()
    public var userAccelerationX = Double()
    public var userAccelerationY = Double()
    public var userAccelerationZ = Double()
    public var rotationX = Double()
    public var rotationY = Double()
    public var rotationZ = Double()
    public var roll = Double()
    public var pitch = Double()
    public var yaw = Double()
    public var w = Double()
    public var wx = Double()
    public var wy = Double()
    public var wz = Double()
    public var time = CLongLong()
    
}
```

导出 .csv 的方法：

```swift
func exportCSV() {
    // 导出文件名称
    let fileName = "SPACE TRACK.csv"
    // 导出路径
    let path = NSURL(fileURLWithPath: NSTemporaryDirectory()).appendingPathComponent(fileName)
    // 表头，注意不要加空格
    var csvHead = "number,gravityX,gravityY,gravityZ,userAccelerationX,userAccelerationY,userAccelerationZ,rotationX,rotationY,rotationZ,roll,pitch,yaw,w,wx,wy,wz,time\n"

    // 将数据写入表头
    for dot in dots {
        csvHead.append("\(dot.number),\(dot.gravityX),\(dot.gravityY),\(dot.gravityZ),\(dot.userAccelerationX),\(dot.userAccelerationY),\(dot.userAccelerationZ),\(dot.rotationX),\(dot.rotationY),\(dot.rotationZ),\(dot.roll),\(dot.pitch),\(dot.yaw),\(dot.w),\(dot.wx),\(dot.wy),\(dot.wz),\(dot.time)\n")
    }

    do {
        try csvHead.write(to: path!, atomically: true, encoding: .utf8)
        print("EXPORT")
    } catch {
        print("ERROR")
    }

    print(path ?? "not found")

}
```

将文件的路径打印出来是：

```yaml
file:///private/var/mobile/Containers/Data/Application/
```

### 获取文件

可以采用  `Share Extension` 的方式获取文件，但实现起来有些复杂。

最简单的方法是：

在 Xcode 里点击「Window - Device and Simulator」找到运行 App 的设备以及 App，选择「Download Container」

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/Swift-导出-csv-文件1.png)

之后可以在工程文件架中找到下载下来的文件，点击「显示包内容」

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/Swift-导出-csv-文件2.png)

在「AppData - tmp」路径下即可找到导出的 .csv 文件

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/Swift-导出-csv-文件3.png)
