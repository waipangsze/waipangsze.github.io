---
layout: post
title: CFD | Rhie-Chow interpolation
categories: [CFD]
tags: [OpenFOAM, HPC, Rhie-Chow]
author: wpsze
date: 2026-09-04 06:08:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/LqIWBKx.png
banner_img: https://i.imgur.com/LqIWBKx.png
---

# Rhie-Chow Interpolation

Instead of storing and updating vector velocity $\mathbf{U}$ directly at the cell faces (a layout known as a **staggered grid**), modern unstructured CFD solvers like OpenFOAM prefer storing primary variables at cell centers (a **collocated grid**) and using Rhie-Chow face interpolation.

This hybrid approach is used for three primary reasons:

---

## 1. Handling Arbitrary Unstructured Meshes

* **Staggered Grid Limitation:** Staggered grids store scalar variables ($p, T$) at cell centers and component velocity vectors ($U_x, U_y, U_z$) on corresponding cell faces pointing normal to those faces. This layout works well on simple, structured Cartesian grids. However, on arbitrary 3D unstructured meshes with non-orthogonal, polyhedral, or skewed cells, defining distinct directional face-staggered velocity components becomes mathematically and algorithmically complex.
* **Collocated Solution:** Storing full 3D velocity vectors $\mathbf{U} = (U_x, U_y, U_z)$ at cell centers allows OpenFOAM to handle complex geometries, polyhedral cells, and mesh movement seamlessly.

---

## 2. Computational Efficiency & Memory Overhead

If full velocity vectors were stored and updated directly as state variables at every face (rather than just cell centers):

* **Memory Footprint:** In 3D finite volume meshes, there are typically **3 to 6 times more faces than cells**. Storing all velocity vectors, matrix coefficients, and gradients at cell faces would significantly increase RAM usage.
* **Matrix Solver Costs:** Solving linear momentum systems at every face center would multiply computational costs and solver time significantly.

---

## 3. Consistency with Transport Equations

Physical scalar transport equations (like enthalpy $T$, turbulence variables $k-\varepsilon$, or species concentration $C$) naturally live at cell centers.

* By updating velocity $\mathbf{U}$ at the same cell centers, momentum is solved using the exact same matrix structures, discretization schemes, and boundary conditions as all other transported scalars.

---

## Summary

The **Rhie-Chow method on a collocated grid** provides the best of both worlds:

| Approach | Primary Storage | Advantages | Drawbacks |
| --- | --- | --- | --- |
| **Pure Staggered Grid** | Face centers | Naturally avoids pressure-velocity decoupling without corrections. | Impractical for arbitrary unstructured or polyhedral meshes. |
| **Pure Collocated Grid (No Rhie-Chow)** | Cell centers | Simple mesh data structures and memory efficiency. | Suffers from checkerboard pressure oscillations. |
| **OpenFOAM Approach** (Collocated + Rhie-Chow) | Cell centers (with face-flux $\phi$) | Highly efficient on unstructured grids; eliminates checkerboard oscillations. | Requires evaluating face-flux correction terms. |


# Rhie-Chow 插值

Rhie-Chow 插值（Rhie-Chow Interpolation）是計算流體力學（CFD）中用於同位網格（Collocated Grid）的一項關鍵技術，旨在消除非物理的棋盤式壓力震盪（Checkerboard Oscillations）。
## 緣起與背景

* 棋盤危機：在同位網格中，速度和壓力存儲在同一個網格中心。如果對控制體介面上直接採用簡單的線性插值計算速度與壓力梯度，離散的連續性方程將無法有效感知相鄰網格間的奇偶跳躍壓力分佈，導致壓力出現如棋盤格般的鋸齒狀劇烈震盪。 
* 歷史突破：1983年，工程師 C.M. Rhie 與 W.L. Chow 在《AIAA Journal》發表了關於渦輪機翼邊緣分離流動的研究論文。他們提出了一種聰明的動量插值修正技巧。 

## 核心思想

* 引入阻尼項：Rhie-Chow 插值在計算單元面上的速度時，除了常规插值外，**還人為加入了一個與「相鄰節點壓力差」相關的附加項**。
* 建立強耦合：這一額外項巧妙地模仿了動量方程的一部分，將壓力梯度直接帶入介面速度的計算中。這使得連續性方程間接包含了鄰近網格的壓力差，從而充當了數值阻尼，徹底平息了棋盤效應。 

## 後續發展

* 雖然原始的 Rhie-Chow 插值取得了巨大成功並被廣泛應用（如主流 CFD 軟體及 OpenFOAM 中），但在極小時間步長或強源項條件下，它仍會暴露出與時間步無關性、過度耗散或虛假震盪等問題。 
* 後續學者（如 Perić、Majumdar、Choi 等）提出了多種改進與廣義化公式，使其在處理複雜網格、動態網格以及局部加密時更加穩健。

# References

1. [Rhie-Chow插值有没有对应的连续的微分方程/积分方程？](https://www.cfd-china.com/topic/735/rhie-chow%E6%8F%92%E5%80%BC%E6%9C%89%E6%B2%A1%E6%9C%89%E5%AF%B9%E5%BA%94%E7%9A%84%E8%BF%9E%E7%BB%AD%E7%9A%84%E5%BE%AE%E5%88%86%E6%96%B9%E7%A8%8B-%E7%A7%AF%E5%88%86%E6%96%B9%E7%A8%8B)
2. [**Collocated grids** | Rhie-Chow](https://sites.fem.unicamp.br/~im450/palestras&artigos/CFD%20of%20Turbulence_Chalmers_Un/chapter_6-1.pdf)
3. [Rhie-Chow interpolation in OpenFOAM](https://www.tfd.chalmers.se/~hani/kurser/OS_CFD_2007/rhiechow.pdf)
4. [Rhie-Chow 插值在OpenFOAM中的实现逻辑](https://zhuanlan.zhihu.com/p/479879683)
   1. Rhie-Chow插值相信大家都不陌生，主要是针对`同位网格（collocated Grid）`中由于速度和压力梯度离散不连续（non-consecutive）导致的`棋盘效应（Checkerboard Problem）`，即速度和压力梯度有间隔状的震荡现象
   2. 基本逻辑可以简单概括为通过不同于一般的面速度插值，**来让最终的压力泊松方程离散关系不再不连续**，并通过求解出来的压力去更新速度和面流量，从而消除上述的棋盘效应。
