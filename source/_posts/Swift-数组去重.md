---
title: Swift 数组去重
date: 2023-08-31 23:31:03
categories:
- [Code, Swift]
tags:
- Swift
math: false
---

### 两组数组去重

```swift
extension Array where Element: Hashable {
    func difference(from other: [Element]) -> [Element] {
        let thisSet = Set(self)
        let otherSet = Set(other)
        return Array(thisSet.symmetricDifference(otherSet))
    }
}
```

用法

```swift
let names1 = ["John", "Paul", "Ringo"]
let names2 = ["Ringo", "Paul", "George"]
let difference = names1.difference(from: names2)
print(difference)

// output:
// ["George", "John"]
```

### 单个数组去重

如果数组内只含有基础类型的数据时，可以写两个for循环遍历，用下标取值做对比；当然也可以用集合 Set（Swift），比较方便快捷。

但是如果需要对 model 数组进行去重，则用不了 Set。

```swift
import UIKit

extension Array {
    // 去重
    func filterDuplicates<E: Equatable>(_ filter: (Element) -> E) -> [Element] {
        var result = [Element]()
        for value in self {
            let key = filter(value)
            if !result.map({filter($0)}).contains(key) {
                result.append(value)
            }
        }
        return result
    }
}
```

用法

```swift
// 基础类型去重
let arrays = ["1", "2", "2", "3", "4", "4"]
let filterArrays = arrays.filterDuplicates({$0})
print(filterArrays)
// output
// ["1", "2", "3", "4"]

// model类的去重
let modelArrays = [Model("1"), Model("1"), Model("2"), Model("3")]
let filterModels = modelArrays.filterDuplicates({$0.name})
print(filterModels)
// output
// [Model("1"), Model("2"), Model("3")]
```

