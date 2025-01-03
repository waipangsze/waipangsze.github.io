---
layout: post
title: ML | PINN | DeepXDE
categories: [ML]
tags: [PINN, AI, DeepONet, ML, DeepXDE]
author: wpsze
date: 2025-01-03 09:30:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/PWLu96W.jpeg
banner_img: https://i.imgur.com/PWLu96W.jpeg
---

# DeepXDE

DeepXDE 是一款开源且高度模块化的科学计算工具，以深度学习为核心，提供多种数据、物理机理及数理融合的模型，如PINN、DeepONet、MFNN等，同时支持多种类型微分方程，如常微分方程、偏微分方程的定义及求解，可有效解决复杂科学计算问题。
基于所提供的深度学习求解模型，DeepXDE具备以下典型的功能特点：

- 高度模块化：DeepXDE提供多种支持调用、组合的模块，如计算域、边界条件、微分方程、神经网络、训练及预测等，方便用户组合构建物理系统；
- 多类微分方程：支持自定义常微分方程、偏微分方程、积分微分方程等来描述具体问题；
- 可扩展性：支持用户结合自身需求添加自定义的数值算法、模型或其他功能；
- 可视化 ：提供一系列丰富的可视化工具，可以帮助用户直观地理解计算结果。

在科学计算领域，DeepXDE的强大功能与高精度求解能力，使其成为国内外知名的科学计算工具之一。截止目前，DeepXDE的下载量已超过40万次，并被全球70多所知名大学、科研机构和企业采用，比如MIT、Stanford、美国西北太平洋国家实验室、通用汽车等。在实际应用中，DeepXDE正在帮助用户快速解决复杂的科学计算问题，为各领域科学研究的进展作出了重要贡献。

- <https://github.com/lululxvi/deepxde>
- <https://deepxde.readthedocs.io/en/latest/user/installation.html#>
- <https://www.paddlepaddle.org.cn/support/news?action=detail&id=3315>

# Install

```console
micromamba create -n deepxde 
micromamba activate /home/wpsze/micromamba/envs/deepxde
micromamba install conda-forge::pytorch-gpu
micromamba install -c conda-forge deepxde
```

# Run

```sh
#!/bin/bash

source /home/wpsze/micromamba/etc/profile.d/mamba.sh
micromamba activate deepxde

# Before running your code, run this shell command to tell torch that there are no GPUs:
#export CUDA_VISIBLE_DEVICES=""

time DDE_BACKEND=pytorch python Helmholtz_equation_2D.py
```

## GPU

```console
nvidia-smi
```

- GPU usage,

```python
import torch
print("--")
print("torch.cuda.memory_allocated: %fMB"%(torch.cuda.memory_allocated(0)/1024/1024))
print("torch.cuda.memory_reserved: %fMB"%(torch.cuda.memory_reserved(0)/1024/1024))
print("torch.cuda.max_memory_reserved: %fMB"%(torch.cuda.max_memory_reserved(0)/1024/1024))
print("--")
print("torch.cuda.memory_allocated: %fGB"%(torch.cuda.memory_allocated(0)/1024/1024/1024))
print("torch.cuda.memory_reserved: %fGB"%(torch.cuda.memory_reserved(0)/1024/1024/1024))
print("torch.cuda.max_memory_reserved: %fGB"%(torch.cuda.max_memory_reserved(0)/1024/1024/1024))
device = torch.device('cuda:0')
free, total = torch.cuda.mem_get_info(device)
mem_used_MB = (total - free) / 1024 ** 2
print("mem_used_MB = ", mem_used_MB)
print("mem_used_GB = ", mem_used_MB/1024)
```

## CPU only

```sh
# Before running your code, run this shell command to tell torch that there are no GPUs:
export CUDA_VISIBLE_DEVICES=""

# This will tell it to use only one GPU (the one with id 0) and so on:
export CUDA_VISIBLE_DEVICES="0"
```

## CPU vs GPU

Test: [Helmholtz equation over a 2D square domain with a hole](https://deepxde.readthedocs.io/en/latest/demos/pinn_forward/helmholtz.2d.neumann.hole.html)

- iterations = 10000
- num_dense_layers = 6 
- num_dense_nodes = 1350


|         | Cores | Training Time     |                 |
|---------|-------|-------------------|-----------------|
| CPU     | 20    | 5809s ~ 97min     |                 |
| GPU     | 10496 | 227s ~ 3.78min    |                 |
| GPU/CPU | 524.8 | 0.03907729385     | 1/0.039 ~ 25.59 |

- CPU: (%CPU=2000)   
  - Intel(R) Xeon(R) Silver 4210R CPU @ 2.40GHz
  - Core(s) per socket:   10
  - Socket(s):            2
- GPU: 3090 (Volatile GPU util ~ 87%)
  - CUDA cores: 10496

# Reproducibility

Completely reproducible results are not guaranteed across PyTorch releases, individual commits, or different platforms. Furthermore, results may not be reproducible between CPU and GPU executions, even when using identical seeds.

However, there are some steps you can take to limit the number of sources of nondeterministic behavior for a specific platform, device, and PyTorch release. First, you can control sources of randomness that can cause multiple executions of your application to behave differently. Second, you can configure PyTorch to avoid using nondeterministic algorithms for some operations, so that multiple calls to those operations, given the same inputs, will produce the same result.

- <https://pytorch.org/docs/stable/notes/randomness.html>

```python
import torch
torch.manual_seed(0)

import numpy as np
np.random.seed(0)

import random
random.seed(0)
```
