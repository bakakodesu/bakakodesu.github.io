---
title: Swift 创建可以自动停止的 Timer
date: 2023-10-06 01:18:48
categories:
- [Code, Swift]
tags:
- Swift
math: false
---

### 在离开页面时停止 Timer

```swift
import SwiftUI

struct TimerView: View {
    @State private var number = 0
    @State private var timer: Timer?
    var body: some View {
        VStack {
            Image("pic")
            Text("\(number)")
                .font(.largeTitle)
        }
        .onAppear {
            self.timer = Timer.scheduledTimer(withTimeInterval: 1, repeats: true) { (_) in
                self.number += 1
                print(self.number)
                
            }
        }
        .onDisappear {
            self.timer?.invalidate()
        }
    }
}
```

### 使用 Combine 提供的 timer publisher

```swift
import SwiftUI
import Combine

struct TimerView: View {
    @State private var number = 0
    let timer = Timer.publish(every: 1, on: .main, in: .common).autoconnect()
    var body: some View {
        VStack {
            Image("pic")
            Text("\(number)")
                .font(.largeTitle)
        }
        .onReceive(timer, perform: { (_) in
             self.number += 1
             print(self.number)
        })
    }
}
```

combine 扩充了 timer 的功能，我们可以直接产生 timer 的 publisher，让它定时发送 value。在此我们设定它每秒触发一次。

```swift
let timer = Timer.publish(every: 1, on: .main, in: .common).autoconnect()
```

由于 timer 的 publisher 必须先连接才能发送 value 的 publisher，所以我们调用 autoconnect，让它可以自动连接，方便之后接收 timer 发送的 value。

在 view 上调用 onReceive(_:perform:)，传入 publisher timer，接收 timer 每秒发送的 value，perform 传入 timer 定時触发的 closure 。

```swift
.onReceive(timer, perform: { (_) in
             self.number += 1
             print(self.number)
})
```

使用 onReceive(_:perform:) 定义 timer 触发的 closure 有特別的好处，当 SwiftUI view 关闭后，timer 也会自动停止，不用另外调用 invalidate。

#### timer publisher 发送的 value 是时间

timer publisher 的类型是 TimerPublisher，从它的定义我们发现它发送的 value 类型是 Date，因此以下例子打印的 value 是 timer 触发的时间。

```swift
.onReceive(timer, perform: { (value) in
    print(value)
})
```

