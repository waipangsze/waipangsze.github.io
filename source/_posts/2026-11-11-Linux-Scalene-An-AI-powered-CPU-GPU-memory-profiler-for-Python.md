---
layout: post
title: Linux | Scalene | An AI-powered CPU+GPU+memory profiler for Python
categories: [Linux]
tags: [conda, Micromamba, Python, profiler]
author: wpsze
date: 2026-11-11 06:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://github.com/plasma-umass/scalene/raw/master/docs/scalene-icon-white.png
banner_img: https://github.com/plasma-umass/scalene/raw/master/docs/scalene-icon-white.png
---

- [Linux | 如何分析可執行程序？例如使用 ldd 或其他工具？](https://waipangsze.github.io/2026/10/20/Linux-analysis-executable-program/)
- [Linux | Python Profiler](https://waipangsze.github.io/2026/03/23/Linux-Python-Profiler/)

---


Scalene 是一个适用于 Python 的高性能 CPU、GPU 和内存分析器，它可以完成许多其他 Python 分析器没有也不能做的事情。**它的运行速度比许多其他分析器快几个数量级，同时提供更详细的信息**。它也是第一个采用人工智能驱动的优化建议的分析器。

# Install

- `conda install conda-forge::scalene`


# Run

- 要运行 Scalene，请使用命令 `scalene program_name.py` 。默认情况下，它会分析 `CPU、GPU 和内存`。如果您只需要一个或部分选项，请使用标志 `--cpu` 、 `--gpu` 和 `--memory` 。例如， `scalene --cpu --gpu program_name.py` 仅分析 CPU 和 GPU。
- 默认情况下，命令 scalene test.py 将打开 Web 界面。要获取 CLI，请添加标志 `--cli` 。

# References

- <https://marketplace.visualstudio.com/items?itemName=EmeryBerger.scalene>
- <https://zhuanlan.zhihu.com/p/654006173>