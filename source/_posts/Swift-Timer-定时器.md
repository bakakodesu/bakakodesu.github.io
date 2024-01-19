---
title: Swift Timer 定时器
date: 2023-08-07 17:21:02
categories:
- [Code, Swift]
tags:
- Swift
math: false
---

```swift
import SwiftUI

struct TimerView: View {
    @State var count1: Int = 0
    @State var count2: Int = 0
    @State var count3: Int = 0
    
    // 创建第一个定时器 timer1 
    @State var timer1: Timer?
    // 创建第二个定时器 timer2
    let timer2 = Timer.publish(every: 1.0, on: .main, in: .common).autoconnect()
    
    var body: some View {
        VStack (spacing: 55) {
            // 调用 timer1 启动方法
            Button(action: {setupTimer()}) {
                Text("start")
            }
            Button(action: {resetTimer()}) {
                Text("reset")
            }
        }
        .onAppear {
            // timer3 创建&启动
            Timer.scheduledTimer(withTimeInterval: 1.0,  repeats: true){ (_) in
                count3 += 1
                print("----> timer3 running \(count3)")
            }
        }
        // timer2 启动！
        .onReceive(timer2) { input in
            count2 += 1
            print("----> timer2 running \(count2)")
        }
    }
    
    private func setupTimer() {
        guard timer1 == nil else { return }
        // timer1 启动！
        timer1 = Timer.scheduledTimer(withTimeInterval: 1.0, repeats: true, block: { _ in
            count1 += 1
            print("----> timer1 running \(count1)")
        })
    }
    
    private func resetTimer() {
        timer1?.invalidate()
        timer1 = nil
    }
}
```
