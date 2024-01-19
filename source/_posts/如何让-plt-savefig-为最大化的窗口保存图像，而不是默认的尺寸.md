---
title: 如何让 plt.savefig() 为最大化的窗口保存图像，而不是默认的尺寸
date: 2023-11-17 16:27:45
categories:
- [Code, Python]
tags:
- Python
math: false
---

在分析心率时，通过如下代码保存的图片是挤在一起的

```python
plt.tight_layout()
plt.savefig("ecg.png", dpi=120)
```

如图

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/ecg1.png)

这也就是使用 `plt.show()` 显示的默认窗口。必须要将窗口放大后才能将内容全部展开，可图片无法放大。

所以参考了：

1. [[How to make plt.savefig() save image for 'maximized' window instead of default size](https://stackoverflow.com/questions/38120826/how-to-make-plt-savefig-save-image-for-maximized-window-instead-of-default-s)](https://stackoverflow.com/questions/38120826/how-to-make-plt-savefig-save-image-for-maximized-window-instead-of-default-s)
2. [Saving Matplotlib graphs to image as full screen](https://stackoverflow.com/questions/32428193/saving-matplotlib-graphs-to-image-as-full-screen)
3. [How to make savefig() save image for 'maximized' window instead of default size](https://stackoverflow.com/questions/10041627/how-to-make-savefig-save-image-for-maximized-window-instead-of-default-size)

这三个问题后，有解决方法：

```python
figure = plt.gcf()  # get current figure
figure.set_size_inches(32, 18)  # set figure's size manually to your full screen (32x18)
plt.savefig('ecg.png', bbox_inches='tight', dpi=120)  # bbox_inches removes extra white spaces
```

保存图片ok

![](https://bakako-1308163928.cos.ap-guangzhou.myqcloud.com/uPic/ecg2.png)
