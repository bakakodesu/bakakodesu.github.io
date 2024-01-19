---
title: AlphaPose 部署
date: 2023-08-16 12:43:11
categories:
- [环境配置, AlphaPose]
tags:
- 计算机视觉
math: false
---

[AlphaPose](https://github.com/MVIG-SJTU/AlphaPose) 为开源项目。项目基于PyTorch深度学习框架，所以要安装PyTorch，一般来说把这两个教程看完，PyTorch就能成功安装了：

- [搭建Pytorch环境(GPU版本，含CUDA、cuDNN)，并在Pycharm上使用（零基础小白向）](https://blog.csdn.net/a922922737/article/details/126620490?ops_request_misc=%7B%22request%5Fid%22%3A%22168343325116800227473919%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=168343325116800227473919&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-2-126620490-null-null.142^v86^insert_down38v5,239^v2^insert_chatgpt&utm_term=pytorch环境搭建&spm=1018.2226.3001.4187)
- [PyCharm与Anaconda超详细安装配置教程](https://www.cnblogs.com/sixuwuxian/p/16756072.html)

之后就是 AlphaPose 的本地部署，copy 源码后一个一个一个一个往 Conda 环境里安装依赖直到程序能正常运行。

需要注意的是在安装依赖过程出现的问题：

1. numpy 版本不能太旧也不能太新了，我用的版本是 1.20.2；
2. 安装 yaml 库的时候，是 `conda install pyyaml`，别安装成了 `conda install yaml`，会报错 `No module named ‘yaml’`；
3. 安装 cython_bbox，`pip install -e git+https://github.com/samson-wang/cython_bbox.git#egg=cython-bbox`；
4. 有一些会调用 C++API 的库在 pip 安装时报错，是因为系统要安装 C++ 环境。用 Microsoft C++ 生成工具（Build Tools）。报错内容 `Microsoft Visual C++ 14.0 or greater is required. Get it with "Microsoft C++ Build Tools": https://visualstudio.microsoft.com/visual-cpp-build-tools/
   ERROR: Could not build wheels for psutil, which is required to install pyproject.toml-based projects`
5. 报错 `No module named ‘detector’` 的话，把 demo_inference.py 拿到根目录下运行即可；
6. 下载目标检测模型 yolov3-spp.weights，并在 yolo 文件夹中新建 data 文件夹并将模型放入：detector/yolo/data/yolov3-spp.weights；
7. 下载 [人体姿态估计模型](https://github.com/MVIG-SJTU/AlphaPose/blob/master/docs/MODEL_ZOO.md) 放入 pretrained_models 文件夹中，使用模型时，需要调用相应的 config 文件，config 文件不用额外下载，源码自带；

部署完成后，cd 到一级文件夹输入下面指令即可运行（更多指令可查看 [Getting Started](https://github.com/MVIG-SJTU/AlphaPose/blob/master/docs/GETTING_STARTED.md)）：

```bash
调用config：configs/halpe_26/resnet/256x192_res50_lr1e-3_1x.yaml
调用模型：pretrained_models/halpe26_fast_res50_256x192.pth

识别图片并保存
python demo_inference.py --cfg configs/halpe_26/resnet/256x192_res50_lr1e-3_1x.yaml --checkpoint pretrained_models/halpe26_fast_res50_256x192.pth --indir examples/demo/ --save_img

识别摄像头
python demo_inference.py --cfg configs/halpe_26/resnet/256x192_res50_lr1e-3_1x.yaml --checkpoint pretrained_models/halpe26_fast_res50_256x192.pth --outdir examples/res --vis --webcam 0
```
