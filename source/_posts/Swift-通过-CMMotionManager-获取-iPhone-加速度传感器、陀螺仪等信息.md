---
title: Swift 通过 CMMotionManager 获取 iPhone 加速度传感器、陀螺仪等信息
date: 2023-09-13 16:07:41
categories:
- [Code, Swift]
tags:
- Swift
- IMU
math: false
---

### 获取 IMU 数据

方法具体代码

```swift
func getData() {

    if motionManager.isDeviceMotionAvailable {
        // 数据更新时间，最短0.01s
        motionManager.deviceMotionUpdateInterval = 0.01
        // timer效果
        motionManager.startDeviceMotionUpdates(to: OperationQueue.main) { data, error in

            gravityX = data?.gravity.x ?? 0
            gravityY = data?.gravity.y ?? 0
            gravityZ = data?.gravity.z ?? 0

            userAccelerationX = data?.userAcceleration.x ?? 0
            userAccelerationY = data?.userAcceleration.y ?? 0
            userAccelerationZ = data?.userAcceleration.z ?? 0

            rotationX = data?.rotationRate.x ?? 0
            rotationY = data?.rotationRate.y ?? 0
            rotationZ = data?.rotationRate.z ?? 0

            roll = data?.attitude.roll ?? 0
            pitch = data?.attitude.pitch ?? 0
            yaw = data?.attitude.yaw ?? 0

            w = data?.attitude.quaternion.w ?? 0
            wx = data?.attitude.quaternion.x ?? 0
            wy = data?.attitude.quaternion.y ?? 0
            wz = data?.attitude.quaternion.z ?? 0

            // 当前时间
            let toc = Date().milliStamp
            // 计时时间 = 当前时间 - 程序开始时间
            time = toc - tic
            number += 1

            let dot = Dot(number: number, gravityX: gravityX, gravityY: gravityY, gravityZ: gravityZ, userAccelerationX: userAccelerationX, userAccelerationY: userAccelerationY, userAccelerationZ: userAccelerationZ, rotationX: rotationX, rotationY: rotationY, rotationZ: rotationZ, roll: roll, pitch: pitch, yaw: yaw, w: w, wx: wx, wy: wy, wz: wz, time: time)

            dots.append(dot)
        }
    }
}
```

dot 的数据结构

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

时间戳（精确到毫秒）

```swift
extension Date {
    // 获取当前 秒级 时间戳 - 10位
    var timeStamp : String {
        let timeInterval: TimeInterval = self.timeIntervalSince1970
        let timeStamp = Int(timeInterval)
        return "\(timeStamp)"
    }

    // 获取当前 毫秒级 时间戳 - 13位
    var milliStamp : CLongLong {
        let timeInterval: TimeInterval = self.timeIntervalSince1970
        let millisecond = CLongLong(round(timeInterval*1000))
        // return "\(millisecond)"
        return millisecond
    }
}
```

### 导出 .csv 文件

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

### 自定义组件

```swift
import SwiftUI

struct HeadCell: View {
    
    @State var title: String
    
    var body: some View{
        HStack(spacing: 0){
            Text(title)
                .font(.system(size: 14.0, weight: .bold, design: .default))
            Spacer()
        }
    }
}

struct BodyCell: View {
    
    @State var title: String
    // MARK: @State var content: Double 就不会刷新....
    @Binding var content: Double
    
    var body: some View{
        HStack(spacing: 0){
            Text(title)
                .font(.system(size: 12.0, weight: .regular, design: .default))
            Spacer()
            Text("\(content)")
                .font(.system(size: 12.0, weight: .regular, design: .default))
            
        }
    }
}

struct TimeCell: View {
    
    @State var title: String
    @Binding var content: CLongLong
    
    var body: some View{
        
        HStack(spacing: 0){
            Text(title)
                .font(.system(size: 12.0, weight: .regular, design: .default))
            Spacer()
            Text("\(content)")
                .font(.system(size: 12.0, weight: .regular, design: .default))
        }
    }
}
```

需要注意的一点是，从 `content` 要实时刷新，所以需要 `@Binding` 声明。

### 主界面代码

```swift
import SwiftUI
import CoreMotion

struct MainView: View {

    let motionManager = CMMotionManager()
    
    @State var gravityX: Double = 0
    @State var gravityY: Double = 0
    @State var gravityZ: Double = 0
    
    @State var userAccelerationX: Double = 0
    @State var userAccelerationY: Double = 0
    @State var userAccelerationZ: Double = 0
    
    @State var rotationX: Double = 0
    @State var rotationY: Double = 0
    @State var rotationZ: Double = 0
    
    @State var roll: Double = 0
    @State var pitch: Double = 0
    @State var yaw: Double = 0
    
    @State var w: Double = 0
    @State var wx: Double = 0
    @State var wy: Double = 0
    @State var wz: Double = 0
    
    @State var tic: CLongLong = 0
    // 记录运行时间  
    @State var time: CLongLong = 0
    
    @State var dots: [Dot] = []
    @State var number = 0
    
    @State var showingAlert = false
    @State var isStart = false
    
    func getData() {
        // 这里不再重复展示
    }
    
    func exportCSV() {
        // 这里不再重复展示
    }
    
    var body : some View {
        
        NavigationView {
            
            List{
                
                TimeCell(title: "时间 ms", content: $time)
                
                Section(header: HeadCell(title: "重力加速度")) {
                    BodyCell(title: "X轴", content: $gravityX)
                    BodyCell(title: "Y轴", content: $gravityY)
                    BodyCell(title: "Z轴", content: $gravityZ)
                }
                
                Section(header: HeadCell(title: "用户加速度")) {
                    BodyCell(title: "X轴", content: $userAccelerationX)
                    BodyCell(title: "Y轴", content: $userAccelerationY)
                    BodyCell(title: "Z轴", content: $userAccelerationZ)
                }
                
                Section(header: HeadCell(title: "角速度")){
                    BodyCell(title: "X轴", content: $rotationX)
                    BodyCell(title: "Y轴", content: $rotationY)
                    BodyCell(title: "Z轴", content: $rotationZ)
                }
                
                Section(header: HeadCell(title: "欧拉角")){
                    BodyCell(title: "俯仰", content: $pitch)
                    BodyCell(title: "摆头", content: $yaw)
                    BodyCell(title: "倾斜", content: $roll)
                }
                
                Section(header: HeadCell(title: "四元数")){
                    BodyCell(title: "w", content: $w)
                    BodyCell(title: "wx", content: $wx)
                    BodyCell(title: "wy", content: $wy)
                    BodyCell(title: "wz", content: $wz)
                }
                
            }
        
            .navigationTitle("Accelerometer")
            .toolbar {
                // 开始以及停止按钮
                ToolbarItem(placement: .navigationBarTrailing) {
                    Button {
                        withAnimation(){
                            isStart.toggle()
                        }
                        tic = Date().milliStamp
                        if isStart {
                            getData()
                        } else {
                            // 停止记录数据
                            motionManager.stopDeviceMotionUpdates()
                        }
                    } label: {
                        Image(systemName: isStart ? "stop.circle.fill" : "restart.circle.fill")
                            .font(.system(size: 14.0, weight: .medium, design: .default))
                            .foregroundColor(.red)
                    }
                }
                // 导出 .csv 文件按钮
                ToolbarItem(placement: .navigationBarTrailing) {
                    Button {
                        exportCSV()
                        showingAlert.toggle()
                    } label: {
                        Image(systemName: "square.and.arrow.up.fill")
                            .font(.system(size: 14.0, weight: .medium, design: .default))
                            .foregroundColor(.red)
                    }
                    .alert(Text("SUCCESS!"), isPresented: $showingAlert) {
                        // Button("Cancel", role: .destructive) { }
                        // Button("Cancel", role: .cancel) { }
                        Button("Confirm") { }
                    } message: {
                        Text("CSV路径 file:///private/var/mobile/Containers/Data/Application/")
                    }
                }
            }
        }
    }
}

struct Dot {
    // 这里不再重复展示
}

extension Date {
    // 这里不再重复展示
}
```

