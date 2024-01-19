---
title: Swift 时间相关的方法
date: 2024-01-13 15:24:12
categories:
- [Code, Swift]
tags:
- Swift
math: false
---

### **时间格式**

```swift
func getDate() -> String {
    //系统时间
    let now = Date()
    let dateFormatter = DateFormatter()
    dformatter.dateFormat = "M月d日"
    return dformatter.string(from: now)
}

func stringToData(string: String) -> Date {
    let dateFormatter = DateFormatter()
    dateFormatter.dateFormat = "yyyy.MM.dd HH:mm:ss"
    let date = dateFormatter.date(from: string)!
    return date
}
```

### 获取当前小时、星期几等

```swift
// 获取当前小时
func getCurrentHour() -> Int {
    let calendar = Calendar.current
    let dateComponets = calendar.dateComponents([Calendar.Component.year, Calendar.Component.month, Calendar.Component.day, Calendar.Component.hour, Calendar.Component.weekday], from: Date())
    return (dateComponets.hour!)
}

//获取星期几
func getCurrentWeekday() -> Int {
    let calendar = Calendar.current
    let dateComponets = calendar.dateComponents([Calendar.Component.year, Calendar.Component.month, Calendar.Component.day, Calendar.Component.hour, Calendar.Component.weekday], from: Date())
    return (dateComponets.weekday! - 1)
}
```

### 计算时间差

```swift
func dateInterval(startTime: String, endTime: String) -> Int{
    let dateFormatter = DateFormatter()
    dateFormatter.dateFormat = "yyyy.MM.dd HH:mm:ss"
    guard let date1 = dateFormatter.date(from: startTime),
          let date2 = dateFormatter.date(from: endTime) else {
        return -1
    }
    let components = NSCalendar.current.dateComponents([.minute], from: date1, to: date2)
    //如果需要返回月份间隔，分钟间隔等等，只需要在dateComponents第一个参数后面加上相应的参数即可，示例如下：
    //let components = NSCalendar.current.dateComponents([.month,.day,.hour,.minute], from: date1, to: date2)
    return components.minute!
}
```

如果是 `date` 格式则可以直接使用

```swift
let components = NSCalendar.current.dateComponents([.minute, .second], from: tic, to: toc)
dateInterval = "\(components.minute!):\(components.second!)"
```

### 星期几显示成中文

```swift
extension String {
    
    func featureWeekday() -> String {
        let dateFormatter = DateFormatter()
        dateFormatter.locale = Locale.current
        dateFormatter.dateFormat = "yyyy-MM-dd HH:mm"
        guard let formatDate = dateFormatter.date(from: self) else { return "" }
        let calendar = Calendar.current
        let weekDay = calendar.component(.weekday, from: formatDate)
        switch weekDay {
        case 1:
            return "星期日"
        case 2:
            return "星期一"
        case 3:
            return "星期二"
        case 4:
            return "星期三"
        case 5:
            return "星期四"
        case 6:
            return "星期五"
        case 7:
            return "星期六"
        default:
            return ""
        }
    }
}
```

### 获取时间戳

```Swift
extension Date {

    /// 获取当前 秒级 时间戳 - 10位
    var timeStamp : String {
        let timeInterval: TimeInterval = self.timeIntervalSince1970
        let timeStamp = Int(timeInterval)
        return "\(timeStamp)"
    }

    /// 获取当前 毫秒级 时间戳 - 13位
    var milliStamp : String {
        let timeInterval: TimeInterval = self.timeIntervalSince1970
        let millisecond = CLongLong(round(timeInterval*1000))
        return "\(millisecond)"
    }
}
```

或者

```swift
let currentDate = Date()
let timeInterval = currentDate.timeIntervalSince1970
let milliseconds = timeInterval * 1000
let timeStamp = CLongLong(milliseconds)
```

### 时间戳转时间

```swift
func timeStampToString(timeStamp: String) -> String{
    let interval:TimeInterval=TimeInterval.init(timeStamp)!
    let date = Date(timeIntervalSince1970: interval)
    let dateformatter = DateFormatter()
    dateformatter.dateFormat = "yyyy-MM-dd HH:mm:ss"
    return dateformatter.string(from: date)
}
```

