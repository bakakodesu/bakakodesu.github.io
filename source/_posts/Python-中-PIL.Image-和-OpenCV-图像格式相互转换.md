---
title: Python 中 PIL.Image 和 OpenCV 图像格式相互转换
date: 2023/08/22 14:45:23
categories:
- [Code, Python]
tags:
- Python
math: false
---

OpenCV 转换成 PIL.Image 格式：

```python
import cv2  
from PIL import Image  
import numpy  
  
img = cv2.imread("1.jpg")  

print(img.shape)
print(type(img))

cv2.imshow("OpenCV",img)  
image = Image.fromarray(cv2.cvtColor(img,cv2.COLOR_BGR2RGB))  
image.show()
#让显示等待键盘输入维持在那里，否则程序跑完就闪退啦！
cv2.waitKey(0)


# output:
# (256, 256, 3)
# <class 'numpy.ndarray'>
```

PIL.Image 转换成 OpenCV 格式：

```python
import cv2  
from PIL import Image  
import numpy  
  
image = Image.open("1.jpg")  

# print(img.shape)		# Image has no attribute 'shape'
print(np.array(img).shape)
print(type(img))

image.show()  
img = cv2.cvtColor(numpy.asarray(image),cv2.COLOR_RGB2BGR)  
cv2.imshow("OpenCV",img)  
cv2.waitKey(0)

# output:
# (256, 256, 3)
# <class 'Image'>
```
