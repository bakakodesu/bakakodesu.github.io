---
title: CoreData 报错 Failed to call designated initializer on NSManagedObject class
date: 2023-10-10 10:16:14
categories:
- [Code, Swift]
tags:
- Swift
math: false
---

- [Resolving 'Failed to call designated initializer on NSManagedObject class'](https://stackoverflow.com/questions/33301250/resolving-failed-to-call-designated-initializer-on-nsmanagedobject-class)

The problem lies not in the code in your question, but in the snippet you included as comments to the other answer:

```swift
var currentCourse = Course()
```

This doesn't just declare `currentCourse` to be of type `Course`, it also creates an instance of the `Course` entity using the standard `init` method. This is expressly not allowed: You must use the designated initialiser: `init(entity entity: NSEntityDescription, insertIntoManagedObjectContext context: NSManagedObjectContext?)`. This is described in the Apple Documentation [here](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObject_Class/index.html#//apple_ref/doc/uid/TP30001171-SW7).

I suspect you do not ever use the instance created by the above var definition, so just define it as being of type `Course?`:

```swift
var currentCourse : Course?
```

Since it is optional, you do not need to set an initial value, though you will need to unwrap the value whenever it is used.
