---
layout: post
title: ML | Automatic Differentiation 
categories: [ML]
tags: [ML, AI, PINN, DeepONet]
author: wpsze
date: 2024-10-22 14:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://upload.wikimedia.org/wikipedia/commons/thumb/3/3c/AutomaticDifferentiationNutshell.png/450px-AutomaticDifferentiationNutshell.png
banner_img: https://upload.wikimedia.org/wikipedia/commons/thumb/3/3c/AutomaticDifferentiationNutshell.png/450px-AutomaticDifferentiationNutshell.png
---

In mathematics and computer algebra, automatic differentiation (auto-differentiation, autodiff, or AD), also called algorithmic differentiation, computational differentiation, is a set of techniques to **evaluate the partial derivative of a function specified** by a computer program.

Automatic differentiation exploits the fact that every computer calculation, no matter how complicated, executes a sequence of elementary arithmetic operations (addition, subtraction, multiplication, division, etc.) and elementary functions (exp, log, sin, cos, etc.). By applying the chain rule repeatedly to these operations, partial derivatives of arbitrary order can be computed automatically, accurately to working precision, and using at most a small constant factor of more arithmetic operations than the original program.

- 計算一個函數**導數**的方法
- 自動微分是利用電腦自動化**求導**的技術

# References

1. [What's Automatic Differentiation?](https://huggingface.co/blog/andmholm/what-is-automatic-differentiation)
2. [自动微分](https://fancyerii.github.io/books/autodiff/)
3. [自动微分](https://indico.ihep.ac.cn/event/17315/attachments/64130/75771/%E8%87%AA%E5%8A%A8%E5%BE%AE%E5%88%86.pdf)
4. [曲率与自动微分](https://vlight.me/2018/04/01/curvature-and-automatic-differentiation/)