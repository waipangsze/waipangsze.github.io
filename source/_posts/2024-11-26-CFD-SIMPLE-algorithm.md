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

- $p$ 為壓力（pressure），
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

SIMPLE (Semi-Implicit Method for Pressure Linked Equations) 是一種數值方法，用於解決不可壓縮Navier-Stokes方程中的速度和壓力耦合問題。

---

### 注意事項

1. **欠鬆弛因子的作用**  
   欠鬆弛因子在每次迭代中減小更新幅度，防止發散，提高算法穩定性。

2. **網格選擇**  
   壓力和速度的網格離散化需滿足 **交錯網格法（staggered grid）**，以避免數值擴散或壓力震盪問題。

3. **收斂條件**  
   選擇合適的殘差範圍和迭代次數限制，確保結果的準確性和計算效率。

SIMPLE 是一種穩健且簡單的方法，適合處理**穩態不可壓縮流體問題**，但在處理瞬態或高雷諾數流體時，可能需要改進版本，如 SIMPLEC 或 PISO。

---

SIMPLE演算法求解過程總結為如下4步：

1. 由給定的初始壓力或上一迭代步壓力求解動量方程，但是求得的速度變數並不一定滿足連續性方程。
2. 根據壓力泊松方程式求解得到壓力。
3. 利用求得的壓力修正速度，使之能滿足連續性方程式.
4. 若速度不滿足動量方程，請回到步驟（1）重複循環，直到滿足為止。

那其他的標量方程式怎麼辦？例如能量方程式、湍流方程式 $k$ 方程式及 $\epsilon$ 方程式等。這些方程式也可以放到上數求解循環中，只要放到速度修正方程式後依序求解即可。

![SIMPLE](https://i.imgur.com/QoJMran.png){width=600}

PISO演算法是在SIMPLE以後發展出來，最初為 **瞬態** **不可壓縮流動** 設計的一種演算法。 PISO演算法與SIMPLE演算法的差異在於速度場由速度修正方程修正後，並沒有直接回到如下動量方程進行迭代循環，而是直接進行更新H矩陣，求解壓力泊松方程，因此動量方程只是初始迭代使用過一次，此後便不再使用，因此相比 SIMPLE 演算法計算量減少，速度更快。

{% note danger %}
由於有方程式的**時間導數項**，**PISO演算法在瞬態流動計算時候比較穩定**。**當時間步長比較小時，時間導數項會遠大於動量方程式對流項、擴散項、源項等其他項，而由於時間倒數項會出現矩陣對角陣上，因此小的時間步長就會使矩陣對角佔優，這種矩陣特性會使方程式的解更加穩定，花費較少的迭代步達到收斂**。

$$
\dfrac{\partial U}{\partial t} = \dfrac{U_p^{i+1} - U_p^{i}}{\Delta t}
$$
{% endnote %}

當然從另一方面來說，由於穩態流動沒有時間導數項，因此若簡單使用SIMPLE演算法就不如PISO演算法穩定，**因此SIMPLE演算法會以使用鬆弛因子的方式人工增加對角矩陣係數，使方程式求解更加穩定**，具體細節這裡就不再多說了。

總的來說吧，PISO演算法比較適合瞬態不可壓縮流體。當時間步長足夠小時（庫朗數<1）時，就不需要人工增加對角矩陣係數，使其對角佔優，只需要做一次動量方程式求解，然後對壓力方程式及速度修正方程式進行迭代循環即可收斂。

![PISO](https://i.imgur.com/2NqUqHH.png){width=600}

# References

1. [第6章 回流问题流动－传热 耦合计算的数值方法](http://staff.ustc.edu.cn/~humaobin/course/cht/ppt/6.0.pdf)
2. [6.4 原始变量顺序求解流场的 压力修正方法](http://staff.ustc.edu.cn/~humaobin/course/cht/ppt/6.4.pdf)
3. [OpenFOAM编程案例 | 14 SIMPLE算法](https://mp.weixin.qq.com/s/vEoW0lTb5QbfTFs7FZwGQQ)
4. [[CFD] The SIMPLE Algorithm (to solve incompressible Navier-Stokes)](https://www.youtube.com/watch?v=OOILoJ1zuiw&ab_channel=FluidMechanics101)
5. [什么是SIMPLE及PISO算法？](https://zhuanlan.zhihu.com/p/126368311?utm_psn=1845836334389919744)
6. [SIMPLE algorithm description](https://www.cfdsupport.com/OpenFOAM-Training-by-CFD-Support/node199.html)