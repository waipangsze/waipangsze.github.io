---
layout: post
title: ML | Genesis
categories: [ML]
tags: [ML, AI, pytorch]
author: wpsze
date: 2024-12-20 17:39:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

# What is Genesis?

![](https://github.com/Genesis-Embodied-AI/Genesis/blob/main/imgs/teaser.png?raw=true){width=600}

- <https://github.com/Genesis-Embodied-AI/Genesis>

Genesis is a physics platform designed for general purpose Robotics/Embodied AI/Physical AI applications. It is simultaneously multiple things:

- A universal physics engine re-built from the ground up, capable of simulating a wide range of materials and physical phenomena.
- A lightweight, ultra-fast, pythonic, and user-friendly robotics simulation platform.
- A powerful and fast photo-realistic rendering system.
- A generative data engine that transforms user-prompted natural language description into various modalities of data.

# micromamba

## Try on CPU only,

```console
micromamba env create -n genesis
micromamba activate genesis
micromamba install pytorch torchvision torchaudio cpuonly -c pytorch
micromamba install anaconda::pip
micromamba install conda-forge::libgl
pip3 install git+https://github.com/cnr-isti-vclab/PyMeshLab
pip install einops timm pillow
pip install genesis-world 
```

and testing,

```console
>>> import genesis as gs
Failed to import pyrender. Rendering will not work.
>>> gs.init(backend=gs.cpu)
[Genesis] [14:56:47] [INFO] ╭─────────────────────────────────────────────────────────────────────────────────────╮
[Genesis] [14:56:47] [INFO] │┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉ Genesis ┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉┈┉│
[Genesis] [14:56:47] [INFO] ╰─────────────────────────────────────────────────────────────────────────────────────╯
[Genesis] [14:56:47] [INFO] Running on [Intel Core Processor (Haswell, no TSX, IBRS)] with backend gs.cpu. Device memory: 62.39 GB.
[Genesis] [14:56:48] [INFO] 🚀 Genesis initialized. 🔖 version: 0.2.0, 🌱 seed: None, 📏 precision: '32', 🐛 debug: False, 🎨 theme: 'dark'.
>>> gs.generate("A drop of water falls on the window")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: module 'genesis' has no attribute 'generate'
>>> 
```

```python
scene = gs.Scene(show_viewer=True)
NameError: name 'trimesh' is not defined
```

export CONDA_INCLUDE_PATH=/EM/wpsze/micromamba/envs/genesis/include/
cd ./ext/LuisaRender
cmake -S . -B build -D CMAKE_BUILD_TYPE=Release -D PYTHON_VERSIONS=3.9 -D LUISA_COMPUTE_DOWNLOAD_NVCOMP=ON -D LUISA_COMPUTE_ENABLE_GUI=OFF -D ZLIB_INCLUDE_DIR=$CONDA_INCLUDE_PATH
cmake --build build -j $(nproc)