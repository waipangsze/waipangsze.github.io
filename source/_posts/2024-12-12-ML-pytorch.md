---
layout: post
title: ML | PyTorch 
categories: [ML]
tags: [ML, AI, pytorch, PINN, DeepONet]
author: wpsze
date: 2024-12-12 10:56:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/AAFS6jD.png
banner_img: https://i.imgur.com/AAFS6jD.png
---

2017 年初由 Facebook 開源的另一套建立在 Torch 之上的深度學習框架 PyTorch 因其語法簡潔優雅、概念直觀和易上手的特性，甫推出便迅速走紅，儼然已成為瓜分深度學習市場的有力競爭者。

PyTorch is an optimized tensor library for deep learning using GPUs and CPUs. Automatic differentiation is done with a tape-based system at both a functional and neural network layer level. This functionality brings a high level of flexibility and speed as a deep learning framework and provides accelerated NumPy-like functionality.

To check if your GPU driver and CUDA/ROCm is enabled and accessible by PyTorch,

```python
import torch

print(torch.__version__)
print("torch.cuda.is_available() = ", torch.cuda.is_available())
print("torch.cuda.device_count() = ", torch.cuda.device_count())
print("torch.cuda.current_device() =", torch.cuda.current_device())
print("torch.cuda.device(0) = ", torch.cuda.device(0))
print("torch.cuda.get_device_name(0) = ", torch.cuda.get_device_name(0))
```

General usage,

```python
# 导⼊包和版本查询
import torch 
import torch.nn as nn 
import torchvision 

print(torch.__version__) 
print(torch.version.cuda) 
print(torch.backends.cudnn.version()) 
print(torch.cuda.get_device_name(0))


# 可复现性在硬件设备（CPU、GPU）不同时，完全的可复现性⽆法保证，即使随机种⼦相同。但是，在同⼀个设备上，应该保证可复现性。具体做法是，在程序开始的时候固定torch的随机种⼦，同时也把numpy的随机种⼦固定。

np.random.seed(0) 
torch.manual_seed(0) 
torch.cuda.manual_seed_all(0) 
torch.backends.cudnn.deterministic = True 
torch.backends.cudnn.benchmark = False
```

# References

1. [Check PyTorch version, CPU and GPU(CUDA) in PyTorch](https://dev.to/hyperkai/check-pytorch-version-cpu-and-gpucuda-in-pytorch-6jk)
2. [Pytorch 高频使用代码集锦](https://mp.weixin.qq.com/s/bcIC27Xo_LAaLUegElzOsg)