---
layout: post
title: ML | set CUDA_HOME in a conda environment
categories: [ML]
tags: [ML, AI, pytorch, NVIDIA, CUDA]
author: wpsze
date: 2025-01-22 22:57:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/uIow9lk.png
banner_img: https://i.imgur.com/uIow9lk.png
---

# set CUDA_HOME in a conda environment

```console
micromamba install -c conda-forge cudatoolkit-dev
which nvcc
nvcc --version
echo $CUDA_HOME
export CUDA_HOME=$CONDA_PREFIX
echo $CUDA_HOME
```

# References

1. [How to set my CUDA_HOME in a conda environment?](https://vinesmsuic.github.io/linux-conda-cudahome/index.html)
2. <https://github.com/conda/conda/issues/7757#issuecomment-967303351>
3. 