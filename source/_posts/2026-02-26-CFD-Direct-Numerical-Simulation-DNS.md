---
layout: post
title: CFD | Direct Numerical Simulation, DNS
categories: [CFD]
tags: [OpenFOAM, HPC, DNS, RANS, URANS, DES, LES, Dissipation Rate]
author: wpsze
date: 2026-02-26 06:08:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

在計算流體力學（CFD）的最高境界——直接數值模擬 (Direct Numerical Simulation, DNS) 中，耗散率（Dissipation Rate）是衡量能量如何從大尺度渦流傳遞至微小尺度並最終轉化為熱能的核心物理量。

# Direct Numerical Simulation

## 1. Incompressible Flow

物理表達式 (不可壓縮流)在不可壓縮流（Incompressible Flow）中，耗散率通常指單位質量的能量耗散率，符號為 $\epsilon$。它直接與流體的速度梯度張量（Velocity Gradient Tensor）相關。其嚴謹的物理定義為：
$$\epsilon = 2\nu S_{ij}S_{ij}$$
其中：$\nu$：分子運動黏度（Kinematic Viscosity）。$S_{ij}$：應變率張量（Strain-rate Tensor），定義為 $S_{ij} = \frac{1}{2} \left( \frac{\partial u_i}{\partial x_j} + \frac{\partial u_j}{\partial x_i} \right)$。在湍流研究中，我們常關注平均耗散率，它代表了能量從動能級聯（Energy Cascade）到達柯爾莫哥洛夫尺度（Kolmogorov scale）後的消失速率。

## 2. 不使用任何湍流模型

DNS 中的計算特點DNS 的核心在於「不使用任何湍流模型」，這使得耗散率的計算具有以下獨特性：全尺度解析 (Full Scale Resolution)為了準確計算 $\epsilon$，DNS 的網格間距 $\Delta x$ 必須達到柯爾莫哥洛夫長度尺度 ($\eta$) 的數量級：通常要求 $\Delta x / \eta \approx 1$ 到 $2$。如果網格不夠細，速度梯度的高頻信息（小渦）會被平滑掉，導致計算出的耗散率嚴重偏低。直接計算 (Direct Calculation)與 RANS 或 LES 不同，DNS 不需要透過「渦黏係數」來估算耗散。做法： 在每一個時步，直接利用數值微分計算節點上的速度梯度 $\partial u_i / \partial x_j$。精度要求： 通常使用高階格式（如譜方法 Spectral Methods 或 6 階以上中心差分）來確保導數計算的精確性。數值耗散 vs. 物理耗散這是 DNS 成敗的關鍵：物理耗散： 由上述公式定義的真實現象。數值耗散： 由數值算法誤差產生的「假耗散」。DNS 的準則： 理想的 DNS 算法必須保證數值耗散遠小於物理耗散。這就是為什麼 DNS 嚴格禁止使用「迎風格式」（Upwind Scheme），因為迎風格式自帶的數值擴散會掩蓋真實的物理耗散，使結果失去科學意義。 

## 3.  可壓縮領域

可壓縮流的擴展當流體進入可壓縮領域（如超音速流）時，耗散率的表達式變得複雜，主要分為兩個部分：螺線型耗散 (Solenoidal Dissipation, $\epsilon_s$)這部分與不可壓縮流相似，源於剪切變形。$$\epsilon_s = \mu \langle \omega_i \omega_i \rangle$$(其中 $\omega$ 為渦度，$\mu$ 為動力黏度)擴張型耗散 (Dilatational Dissipation, $\epsilon_d$)這是可壓縮流特有的，源於流體的體積壓縮與膨脹（如衝擊波或聲波）：$$\epsilon_d = \frac{4}{3}\mu \langle (\nabla \cdot \mathbf{u})^2 \rangle$$總耗散在可壓縮 DNS 中，總耗散為：$$\epsilon_{total} = \epsilon_s + \epsilon_d$$計算難點： 在高馬赫數下，為了捕捉劇烈的壓縮（衝擊波），DNS 需要極高的空間解析度，且必須處理壓力、密度與溫度的耦合耗散（即熵產生的部分）。

---

# Kinetic Energy Equation

1. 從動量方程開始 (Momentum Equation)考慮不可壓縮流的 N-S 方程（忽略外力）：

$$\rho \left( \frac{\partial u_i}{\partial t} + u_j \frac{\partial u_i}{\partial x_j} \right) = -\frac{\partial p}{\partial x_i} + \frac{\partial \tau_{ij}}{\partial x_j}$$

其中，對於牛頓流體，黏滯應力張量（Viscous Stress Tensor）為：

$$\tau_{ij} = 2\mu S_{ij} = \mu \left( \frac{\partial u_i}{\partial x_j} + \frac{\partial u_j}{\partial x_i} \right)$$

2. 推導動能方程式 (Kinetic Energy Equation)為了得到單位體積動能 $k = \frac{1}{2} \rho u_i u_i$ 的演化方程，我們將動量方程全體乘以速度分量 $u_i$：

$$u_i \left[ \rho \left( \frac{\partial u_i}{\partial t} + u_j \frac{\partial u_i}{\partial x_j} \right) \right] = u_i \left[ -\frac{\partial p}{\partial x_i} + \frac{\partial \tau_{ij}}{\partial x_j} \right]$$

利用鏈式法則簡化左項，得到動能隨體導數：

$$\rho \frac{D (\frac{1}{2} u_i u_i)}{Dt} = -u_i \frac{\partial p}{\partial x_i} + u_i \frac{\partial \tau_{ij}}{\partial x_j}$$

3. 展開黏滯項 (Viscous Term)這是推導耗散率的最關鍵步驟。我們利用乘積微分法則展開右邊最後一項（黏滯力做功項）：

$$u_i \frac{\partial \tau_{ij}}{\partial x_j} = \frac{\partial (u_i \tau_{ij})}{\partial x_j} - \tau_{ij} \frac{\partial u_i}{\partial x_j}$$

第一項 $\frac{\partial (u_i \tau_{ij})}{\partial x_j}$：稱為「黏滯擴散項」（Viscous Diffusion）。它代表黏滯力在空間中傳遞能量，並不消耗能量，只是能量的「搬運工」。
第二項 $-\tau_{ij} \frac{\partial u_i}{\partial x_j}$：這就是**能量轉換的核心**。由於黏滯應力張量 $\tau_{ij}$ 與速度梯度 $\frac{\partial u_i}{\partial x_j}$ 的乘積總是傾向於將動能提取出來，這就是耗散項。

4. 耗散率的定義 (Definition of $\epsilon$)將耗散項進一步展開。由於 $\tau_{ij}$ 是對稱張量，我們可以將速度梯度分解為對稱部分（應變率 $S_{ij}$）和反對稱部分（旋度 $W_{ij}$）：$$\frac{\partial u_i}{\partial x_j} = S_{ij} + W_{ij}$$因為 $\tau_{ij} W_{ij} = 0$（對稱乘以反對稱張量之縮併為零），耗散項簡化為：

$$\Phi = \tau_{ij} S_{ij} = (2\mu S_{ij}) S_{ij} = 2\mu S_{ij} S_{ij}$$

在 DNS 中，我們通常定義單位質量的耗散率 $\epsilon$：

$$\epsilon = \frac{\Phi}{\rho} = 2\nu S_{ij} S_{ij}$$

其中 $\nu = \mu / \rho$ 是分子運動黏度。