---
layout: post
title: Maths | Boundary Conditions
categories: [Maths]
tags: [NWP, AI, ML, PDE]
author: wpsze
date: 2024-10-25 10:22:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://upload.wikimedia.org/wikipedia/commons/thumb/2/2c/Boundary_value_problem-en.svg/1920px-Boundary_value_problem-en.svg.png
banner_img: https://upload.wikimedia.org/wikipedia/commons/thumb/2/2c/Boundary_value_problem-en.svg/1920px-Boundary_value_problem-en.svg.png
---

In the study of differential equations, a boundary-value problem is a differential equation subjected to constraints called boundary conditions. A solution to a boundary value problem is a solution to the differential equation which also satisfies the boundary conditions.

Summary of boundary conditions for the unknown function, 
$y$, constants $c_0$ and $c_1$ specified by the boundary conditions, and known scalar functions $f$ and $g$ pecified by the boundary conditions.

|    Name   | Form on 1st part of boundary | Form on 2nd part of boundary |
|:---------:|:----------------------------:|:----------------------------:|
| Dirichlet |            $y = f$           |                              |
|  Neumann  | $\dfrac{\partial y}{\partial n} = f$|                              |
|   Robin   |   $c_0 y + c_1 \dfrac{\partial y}{\partial n} = f$   |                              |
|   Mixed   |            $y = f$            |   $c_0 y + c_1 \dfrac{\partial y}{\partial n} = g$   |
|   Cauchy  | both $y = f$ and $\dfrac{\partial y}{\partial n} = g$ |                              |

- 根據條件的形式，邊值條件分以下三類
  - 第一類邊值條件：也稱為狄利克雷邊界條件，直接描述物理系統邊界上的物理量，例如振動的弦兩端與平衡位置的距離；
  - 第二類邊值條件：也稱為諾伊曼邊界條件，描述物理系統邊界上物理量垂直邊界的導數的情況，例如導熱細杆端點的熱流；
  -   第三類邊值條件：物理系統邊界上物理量與垂直邊界導數的線性組合，例如，細杆端點的自由冷卻，溫度、熱流均不確定，但是二者的關係確定，即可列出二者線性組合而成的邊值條件。

| Boundary type | Formal Name | Mathematical form       |
|---------------|-------------|-------------------------|
| Type 1        | Dirichlet   | $h(x,y,z,t) = constant$ |
| Type 2        | Neumann     | $\dfrac{\partial h}{\partial n} = constant$                  |
| Type 3        | Cauchy      | $\dfrac{\partial h}{\partial n} + c h= constant$                     |

邊值條件也可以根據邊值問題對應的微分算子來分類：若是使用橢圓算子，則問題為橢圓邊值問題；使用雙曲線算子，則問題為雙曲線邊值問題。依微分算子還可以將問題再細分為線性及非線性等。

