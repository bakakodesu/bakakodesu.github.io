---
title: OpenCV CAP_PROP_FRAME_COUNT 获取视频帧数问题
date: 2023-08-18 15:50:32
categories:
- [Code, Python]
tags:
- 计算机视觉
- Python
math: false
---

OpenCV 读取视频，可以通过属性 `CAP_PROP_FRAME_COUNT` 获取视频的总帧数，但是有些视频通过该属性获取的帧数和实际遍历整个视频的帧数不一样。

```python
import cv2 as cv
video = 'video.mp4'
cap = cv.VideoCapture(video)
frames = cap.get(cv.CAP_PROP_FRAME_COUNT)    # 通过属性获取帧数
count = 0    # 用于计算视频的实际帧数
while cap.isOpened():
    ret, frame = cap.read()
    if not ret:
        break
    count += 1
cap.release()
print(frames)
print(count)

# output: 
# 432
# 541
```

出现这种原因是，视频中包含了损坏的或不能被 OpenCV 解码的帧，OpenCV 就会跳过这些帧，因此造成通过属性 `CAP_PROP_FRAME_COUNT` 和实际读取的帧数不一样。
