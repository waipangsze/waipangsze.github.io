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

```console
micromamba env create -n genesis
micromamba activate genesis
micromamba install pytorch torchvision torchaudio cpuonly -c pytorch
micromamba install anaconda::pip
pip3 install git+https://github.com/cnr-isti-vclab/PyMeshLab
pip install genesis-world 
```

and,

```python
import genesis as gs
gs.init(backend=gs.cpu)
gs.generate("A drop of water falls on the window")
```