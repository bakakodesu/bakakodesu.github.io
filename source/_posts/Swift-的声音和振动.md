---
title: Swift 的声音和振动
date: 2024-01-19 15:38:33
categories:
- [Code, Swift]
tags:
- Swift
math: false
---

### 声音

```swift
import AVFoundation

class SoundPlayer {
    var soundIdIndex = 1000
    func playSound() {
        let soundID = SystemSoundID(soundIdIndex)
        AudioServicesPlaySystemSound(soundID)
    }
}
```

使用方法为

```swift
let player = SoundPlayer()
player.playSound()
```

声音 id 可参考 [iOSSystemSoundsLibrary](https://github.com/TUNER88/iOSSystemSoundsLibrary)

### 振动

```swift
import AVFoundation

enum Vibration {
    
    case error
    case success
    case warning
    case light
    case medium
    case heavy
    @available(iOS 13.0, *)
    case soft
    @available(iOS 13.0, *)
    case rigid
    case selection
    
    public func vibrate() {
        switch self {
        case .error:
            UINotificationFeedbackGenerator().notificationOccurred(.error)
        case .success:
            UINotificationFeedbackGenerator().notificationOccurred(.success)
        case .warning:
            UINotificationFeedbackGenerator().notificationOccurred(.warning)
        case .light:
            UIImpactFeedbackGenerator(style: .light).impactOccurred()
        case .medium:
            UIImpactFeedbackGenerator(style: .medium).impactOccurred()
        case .heavy:
            UIImpactFeedbackGenerator(style: .heavy).impactOccurred()
        case .soft:
            if #available(iOS 13.0, *) {
                UIImpactFeedbackGenerator(style: .soft).impactOccurred()
            }
        case .rigid:
            if #available(iOS 13.0, *) {
                UIImpactFeedbackGenerator(style: .rigid).impactOccurred()
            }
        case .selection:
            UISelectionFeedbackGenerator().selectionChanged()
        
        }
    }
}
```

使用方法

```swift
Vibration.rigid.vibrate()
```

