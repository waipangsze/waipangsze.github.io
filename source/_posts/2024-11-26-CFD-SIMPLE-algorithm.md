---
layout: post
title: "CFD | SIMPLE 算法"
categories: [CFD]
tags: [OpenFOAM,HPC]
author: wpsze
date: 2024-11-26 13:36:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/fFT42Yk.png
banner_img: https://i.imgur.com/fFT42Yk.png
---

# **不可壓縮 Navier-Stokes 方程**

對於不可壓縮Navier-Stokes方程，可以表示為：

## **動量方程：**  

$$
\rho \frac{\partial \mathbf{u}}{\partial t} + \rho (\mathbf{u} \cdot \nabla) \mathbf{u} = -\nabla p + \mu \nabla^2 \mathbf{u} + \mathbf{f}
$$

## **穩狀:**

$$
\frac{\partial \mathbf{u}}{\partial t} = 0
$$

## **連續方程：**  
$$
\nabla \cdot \mathbf{u} = 0
$$

式中有 4 個待求的物理量：

- $\mathbf{u}_x$：在 $x$ 方向的速度分量，
- $\mathbf{u}_y$：在 $y$ 方向的速度分量，
- $\mathbf{u}_z$：在 $z$ 方向的速度分量，
- $p$：壓力。

其中：

- $p$ 為運動壓力（dynamic pressure），
- $\mu$ 為運動黏度（dynamic viscosity），
- $\rho$ 為流體密度，
- $\mathbf{f}$ 為體積力，例如重力或外加力。

在求解不可壓縮 Navier-Stokes方 程時，會遇到以下兩個主要困難：

1. **壓力的顯式求解**：壓力 $\nabla p$ 隱藏在動量方程中，與速度場 $\mathbf{u}$ 相耦合，沒有顯式的壓力求解方式。
2. **連續性約束**：從動量方程中求解出的速度場 $\mathbf{u}$ 不一定滿足連續方程 $\nabla \cdot \mathbf{u} = 0$ 的不可壓縮性條件。

# SIMPLE 演算法

SIMPLE（Semi-Implicit Method for Pressure-Linked Equations）演算法是針對上述問題提出的一種數值求解方法，主要透過壓力校正來分解耦合問題。其步驟如下：

1. **動量方程離散化：**  
   將動量方程寫成矩陣形式：
   $$
   [M] \mathbf{u} = \mathbf{b}
   $$
   其中 $[M]$ 是係數矩陣，$\mathbf{u}$ 是速度場，$\mathbf{b}$ 是包含壓力梯度項和其它源項的向量。

---

在不可壓縮Navier-Stokes方程中，壓力的直接求解通常利用 **壓力 Poisson 方程**。該方程是從動量方程與連續性條件組合推導而來，用於確保速度場滿足不可壓縮條件（$\nabla \cdot \mathbf{u} = 0$）。推導過程如下：

---

## 壓力 Poisson 方程的推導

1. **連續方程：**
   $$
   \nabla \cdot \mathbf{u} = 0
   $$

2. **動量方程：**
   $$
   \rho \frac{\partial \mathbf{u}}{\partial t} + \rho (\mathbf{u} \cdot \nabla) \mathbf{u} = -\nabla p + \mu \nabla^2 \mathbf{u} + \mathbf{f}
   $$

   將動量方程兩側對空間做散度運算，得到：
   $$
   \nabla \cdot \left[ \rho \frac{\partial \mathbf{u}}{\partial t} + \rho (\mathbf{u} \cdot \nabla) \mathbf{u} \right] = -\nabla^2 p + \nabla \cdot (\mu \nabla^2 \mathbf{u}) + \nabla \cdot \mathbf{f}
   $$

3. **利用連續性約束：**
   假設 $\mathbf{u}$ 不完全滿足連續性，為了修正壓力，考慮壓力與速度的相互影響，可得壓力 Poisson 方程的通用形式：
   $$
   \nabla^2 p = \nabla \cdot \left[ \rho (\mathbf{u} \cdot \nabla) \mathbf{u} \right] - \rho \frac{\partial (\nabla \cdot \mathbf{u})}{\partial t} + \nabla \cdot \mathbf{f}.
   $$

4. **穩態情況：**
   當系統處於穩態且外力項可忽略時，簡化為：
   $$
   \nabla^2 p = \rho \nabla \cdot \left[ (\mathbf{u} \cdot \nabla) \mathbf{u} \right].
   $$

---

## 使用壓力 Poisson 方程的目標

- **修正壓力場：** 確保修正後的速度場滿足不可壓縮條件。
- **耦合速度與壓力：** 將壓力從動量方程顯性分離，通過 壓力 Poisson 方程 **解壓力**後，**代入動量方程修正速度場**。

---

壓力 Poisson 方程的解法通常基於離散化數值技術，例如有限差分法、有限體積法等，通過求解壓力校正場 $p'$ 來逐步逼近最終滿足 $\nabla \cdot \mathbf{u} = 0$ 的速度場。

SIMPLE (Semi-Implicit Method for Pressure Linked Equations) 是一種數值方法，用於解決不可壓縮Navier-Stokes方程中的速度和壓力耦合問題。以下是SIMPLE演算法的詳細步驟：

---

### 1. 初始條件設定

- 設定初始的速度場 $\mathbf{u}^0$ 和壓力場 $p^0$。
- 設定收斂條件，例如速度或壓力的殘差限值。

---

### 2. 解動量方程（假設初始壓力場）
利用上一迭代的壓力場 $p^0$ 或初始壓力場，解動量方程以得到暫時速度場（intermediate velocity field） $\mathbf{u}^*$：

$$
[A] \mathbf{u}^* = \mathbf{b} - \nabla p^0
$$

其中：

- $[A]$ 是動量方程離散化後的係數矩陣；
- $\mathbf{b}$ 包含外力和非線性項（如對流項）的源項。

由於使用的壓力場 $p^0$ 是近似的，解出的速度場 $\mathbf{u}^*$ 不一定滿足不可壓縮條件 $\nabla \cdot \mathbf{u} = 0$。

---

### 3. 壓力校正方程

引入壓力校正項 $p' = p - p^0$，修正壓力場為：
$$
p = p^0 + p'
$$

修正後的速度場為：

$$
\mathbf{u} = \mathbf{u}^* + \mathbf{u}'
$$

將校正速度場代入連續性方程（$\nabla \cdot \mathbf{u} = 0$），得到壓力校正方程：

$$
\nabla^2 p' = \frac{\rho}{\Delta t} \nabla \cdot \mathbf{u}^*
$$

這是一個泊松方程，描述壓力修正項 $p'$ 與暫時速度場 $\mathbf{u}^*$ 的關係。

---

### 4. 壓力場更新

用壓力校正項 $p'$ 修正壓力場：

$$
p^{n+1} = p^n + \alpha_p p'
$$

其中，$\alpha_p$ 是 **壓力欠鬆弛因子** （通常取值範圍為 $0.2 - 0.5$），用於提高收斂穩定性。

---

### 5. 速度場更新

用壓力校正項 $p'$ 修正暫時速度場 $\mathbf{u}^*$：

$$
\mathbf{u}^{n+1} = \mathbf{u}^* + \beta_u \nabla p'
$$

其中，$\beta_u$ 是速度欠鬆弛因子。

---

### 6. 檢查收斂

計算速度場和壓力場的殘差，例如：

- 速度殘差：$\|\nabla \cdot \mathbf{u}\|$
- 壓力殘差：$\|p^{n+1} - p^n\|$

如果殘差小於設定的閾值，演算法結束，否則返回第2步繼續迭代。

---

### 簡化的流程圖

1. 初始值設置：$\mathbf{u}^0, p^0$
2. 解動量方程，得到暫時速度場 $\mathbf{u}^*$
3. 解壓力校正泊松方程，得到 $p'$
4. 更新壓力場：$p = p^0 + p'$
5. 更新速度場：$\mathbf{u} = \mathbf{u}^* + \mathbf{u}'$
6. 檢查收斂，若未收斂，重複迭代。

---

### 注意事項

1. **欠鬆弛因子的作用**  
   欠鬆弛因子（$\alpha_p, \beta_u$）在每次迭代中減小更新幅度，防止發散，提高算法穩定性。

2. **網格選擇**  
   壓力和速度的網格離散化需滿足 **交錯網格法（staggered grid）**，以避免數值擴散或壓力震盪問題。

3. **收斂條件**  
   選擇合適的殘差範圍和迭代次數限制，確保結果的準確性和計算效率。

SIMPLE 是一種穩健且簡單的方法，適合處理**穩態不可壓縮流體問題**，但在處理瞬態或高雷諾數流體時，可能需要改進版本，如 SIMPLEC 或 PISO。

# References

1. [OpenFOAM编程案例 | 14 SIMPLE算法](https://mp.weixin.qq.com/s/vEoW0lTb5QbfTFs7FZwGQQ)
2. [[CFD] The SIMPLE Algorithm (to solve incompressible Navier-Stokes)](https://www.youtube.com/watch?v=OOILoJ1zuiw&ab_channel=FluidMechanics101)
