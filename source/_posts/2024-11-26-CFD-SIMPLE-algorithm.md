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
2. 當採用分離式解法求解速度場時，控制方程組中沒有關於壓力的獨立約束方程，直接對離散方程進行解時，無法單獨求得壓力場。
3. **連續性約束**：從動量方程中求解出的速度場 $\mathbf{u}$ 不一定滿足連續方程 $\nabla \cdot \mathbf{u} = 0$ 的不可壓縮性條件。
4. 在同位網格上進行方程式離散時，壓力梯度項的離散會產生不合理壓力場的偵測問題。

# SIMPLE 演算法

SIMPLE（Semi-Implicit Method for Pressure-Linked Equations）演算法是針對上述問題提出的一種數值求解方法，主要透過壓力校正來分解耦合問題。
壓力修正演算法也稱預測-校正演算法，其實質為迭代方法。
其步驟如下：

## 1. **三維問題動量方程的離散方程化：**  

三維問題動量方程的離散方程化
$$
\begin{cases}
a_e u_e = \sum a_{nb} u_{nb} + b + A_e (p_{P} - p_E) \\
a_n v_n = \sum a_{nb} v_{nb} + b + A_n (p_{P} - p_N) \\
a_t w_t = \sum a_{nb} w_{nb} + b + A_t (p_{P} - p_T)
\end{cases}
\quad (1)
$$
其中 $A$ 為壓力作用在界面面積

![交錯網格](https://i.imgur.com/Va046Ku.png)

將動量方程寫成矩陣形式：
$$
[M] \mathbf{u} = \mathbf{b}
$$
其中 $[M]$ 是係數矩陣，$\mathbf{u}$ 是速度場，$\mathbf{b}$ 是包含壓力梯度項和其它源項的向量。

## 2. **求 不完善的速度場$\vec{V^*}$:** 
   
以一個假定的壓力場 $p^*$ 為基礎，帶入動量方程的離散方程，可求得不完全的速度場 $\vec{V^*}$
$$
\begin{cases}
a_e u_e^* = \sum a_{nb} u_{nb}^* + b + A_e (p_{P}^* - p_E^*) \\
a_n v_n^* = \sum a_{nb} v_{nb}^* + b + A_n (p_{P}^* - p_N^*) \\
a_t w_t^* = \sum a_{nb} w_{nb}^* + b + A_t (p_{P}^* - p_T^*)
\end{cases}
\quad (2)
$$

## 3. **求 速度的修正量 $\vec{V}'$:**
   
由壓力的修正量 $p'$ (未知) 求速度的修正量 $\vec{V}'$

以 $p'$ 代表壓力的修正量，$p^*$ 表示假定的壓力場，則修正後的壓力為

$$ p = p^* + p'$$

相應修正後的速度場為：

$$
\begin{cases}
u = u^* + u' \\
v = v^* + v' \\
w = w^* + w'
\end{cases}
$$

（1）（2）式相減，可以得到壓力修正值和速度修正值的關係：

$$
\begin{cases}
a_e u_e' = \sum a_{nb} u_{nb}' + A_e (p_{P}' - p_E') \\
a_n v_n' = \sum a_{nb} v_{nb}' + A_n (p_{P}' - p_N') \\
a_t w_t' = \sum a_{nb} w_{nb}' + A_t (p_{P}' - p_T')
\end{cases}
\quad (3)
$$

成矩陣形式：
$$
[M] \mathbf{u'} = \mathbf{b'}
$$

可以看出，由修正壓力值可以直接求出速度修正值。

為簡化計算，只保留第一項，忽略間接影響，即假設係數 $a_{nb}=0$，得速度修正方程式：

$$
\begin{cases}
a_e u_e' = A_e (p_{P}' - p_E') \\
a_n v_n' = A_n (p_{P}' - p_N') \\
a_t w_t' = A_t (p_{P}' - p_T')
\end{cases}
\quad (4)
$$

{% note primary %}
- 這裡，我們近似地忽略四周鄰點速度修正值的影響，即設係數 $a_{nb}=0$（這就是**半隱式方法**的意義，**保留這部分就是全隱**了）
- **Explicit** vs **Implicit** vs **Semi-Implicit (not fully implicit)**
- In a **fully implicit** system, every change in pressure at one node would instantaneously account for the velocity changes at all neighboring nodes.
{% endnote %}

因此，修正後的速度為：

$$
\begin{cases}
u_e = u^* + u_e' = u^* + d_e (p_{P}' - p_E') \\
u_n = u^* + u_n' = u_e^* + d_n (p_{P}' - p_N') \\
u_t = u^* + u_t' = u_t^* + d_t (p_{P}' - p_T')
\end{cases}
\quad (5)
$$

其中：

$$
d_e = \frac{A_e}{a_e}, \quad d_n = \frac{A_n}{a_n}, \quad d_t = \frac{A_t}{a_t}
\quad (6)
$$

**修正後的速度 $\vec{V}$** 只取決於 $p'$ !

> **但是，$p'$ 的值仍然未知，如何才能算出它呢？**

## 4. **求 壓力的修正量 $p'$:**

把修正後的速度場 $\vec{V}$ **代入連續性方程式**看其是否滿足，從而**得到壓力的修正方程式**。需要注意的是，這裡連續性方程式的離散是針對修正後的速度場而不是不完善的速度場的。

連續性方程式離散：

$$
\int_{b}^{t} \int_{n}^{s} \int_{e}^{w} \left[ \frac{\partial (\rho u)}{\partial x} + \frac{\partial (\rho v)}{\partial y} + \frac{\partial (\rho w)}{\partial z} \right] \, dx \, dy \, dz = 0 \quad (7)
$$

得到：

$$
\left[ (\rho u)_e - (\rho u)_w \right] \Delta y \Delta z + \left[ (\rho u)_n - (\rho u)_s \right] \Delta x \Delta z + \left[ (\rho u)_t - (\rho u)_b \right] \Delta x \Delta y = 0 \quad (8)
$$


將 **修正後的速度 $\vec{V}$** 代入，得到關於修正量 $p'$ 的代數方程，可得：

$$
a_P p_P' = a_W p_W' + a_E p_E' + a_S p_S' + a_N p_N' + a_B p_B' + a_T p_T' + b' \quad (8)
$$

上式為求壓力修正值 $p'$ 的離散方程，方程中 $b'$ 是由於不完善的速度場所所導致的連續性不平衡量，若 $\vec{V^*}$ 滿足連續性方程式的話，那麼 $b'=0$，從而不需要對壓力進行進一步的修正。因此，$b'=0$ 可以作為速度場迭代是否收斂的判定標準。

根據 $p'$ 計算而得到的 $\vec{V'}$ 能使 $\vec{V} = \vec{V^*} + \vec{V'}$ 滿足連續性方程，於是就**作為本次迭代上速度場的解**，並以其改進離散方程式的係數，開始下一步迭代計算。


## 注意事項

1. **亞鬆弛因子的作用**  
   1. 對於壓力，由於在速度修正值公式中，略去了鄰點的影響，用 $p'$ 解得的修正速度是合適的，但壓力修正值 $p'$ 本身被誇大了，因而要進行亞鬆弛處理。
      1. $p = p^* + \alpha_p p'$ 
   2. 亞鬆弛因子在每次迭代中減小更新幅度，防止發散，提高算法穩定性。
   3. 我們其實是在說：“數學計算告訴我應該走 10 步，**但因為我採用了‘半隱式’的捷徑**，所以我只相信結果到足以走 3 步。”
2. **網格選擇**  
   1. 壓力和速度的網格離散化需滿足 **交錯網格法（staggered grid）**，以避免數值擴散或壓力震盪問題。
3. **收斂條件**  
   1. 選擇合適的殘差範圍和迭代次數限制，確保結果的準確性和計算效率。

SIMPLE 是一種穩健且簡單的方法，適合處理**穩態不可壓縮流體問題**，但在處理瞬態或高雷諾數流體時，可能需要改進版本，如 **SIMPLEC** 或 **PISO**。

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

# Q: 動量方程的離散方程 Linearization ?

Yes, this is exactly where the "fun" (and the difficulty) of the Navier-Stokes equations lies! This is the Non-Linearity of fluid dynamics.

In the advection term $\rho u \frac{\partial u}{\partial x}$, the velocity $u$ is both the **carrier (the flow moving the fluid)** and the **quantity being carried (the momentum)**.

$$
\begin{cases}
\rho u \frac{\partial u}{\partial x} = \mu \frac{\partial^2 u}{\partial x^2} + S \\
\rho u_i \left( \frac{u_{i+1} - u_{i-1}}{2\Delta x} \right) = \mu \left( \frac{u_{i+1} - 2u_i + u_{i-1}}{\Delta x^2} \right) + S_i \\
\left( -\frac{\rho u_i}{2\Delta x} - \frac{\mu}{\Delta x^2} \right) u_{i-1} + \left( \frac{2\mu}{\Delta x^2} \right) u_i + \left( \frac{\rho u_i}{2\Delta x} - \frac{\mu}{\Delta x^2} \right) u_{i+1} = S_i \\
C = \frac{\rho u_i}{2\Delta x} \mbox{(Convection coefficient)} \\
D = \frac{\mu}{\Delta x^2} \mbox{(Diffusion coefficient)} \\
\underbrace{(-C - D)}_{a_W} u_{i-1} + \underbrace{(2D)}_{a_P} u_i + \underbrace{(C - D)}_{a_E} u_{i+1} = S_i
\end{cases}
$$

## 1. The Linearization Strategy

Computers cannot solve a system of equations where the unknowns are multiplied by each other (like $u_i \cdot u_{i+1}$) in a single direct step. We must linearize the equation to create the matrix $\mathbf{A} \mathbf{u} = \mathbf{b}$.

In the SIMPLE algorithm, we use the values from the previous iteration (notated with a superscript $\circ$) to calculate the coefficients, while the current iteration's values remain the unknowns.

The Discretized Term:

$$\text{Advection} \approx \rho u_i^\circ \left( \frac{u_{i+1} - u_{i-1}}{2\Delta x} \right)$$

Now, because $u_i^\circ$ is just a known number from the last check, the equation is linear with respect to the unknowns $u_{i+1}$ and $u_{i-1}$.

## 2. Impact on the Coefficients

Let's look at how this changes our matrix coefficients $a_W$ and $a_E$:

- Convection Strength: $C = \frac{\rho u_i^\circ}{2\Delta x}$
- Diffusion Strength: $D = \frac{\mu}{\Delta x^2}$
- The Coefficients in the Matrix:

$$a_W = -C - D = -\left( \frac{\rho u_i^\circ}{2\Delta x} \right) - \frac{\mu}{\Delta x^2}$$

$$a_E = C - D = \left( \frac{\rho u_i^\circ}{2\Delta x} \right) - \frac{\mu}{\Delta x^2}$$

## 3. Why This "Feedback Loop" Matters

Because the coefficients $C$ depend on $u$, the matrix $\mathbf{A}$ actually changes shape every time you run a loop in the SIMPLE algorithm.

- Iter 1: Assume $u = 0$ everywhere. Calculate $C$, build Matrix $\mathbf{A}_1$, solve for $u_1$.
- Iter 2: Use $u_1$ to recalculate $C$. Build a new Matrix $\mathbf{A}_2$, solve for $u_2$.
- Iter 3: Use $u_2$ to update $C$ again...

This is why CFD is an iterative process. We aren't just solving for pressure-velocity coupling; we are also solving for the non-linearity of the velocity field itself.

## 4. The Stability Risk (Diagonal Dominance)

Recall that for a matrix solver to be stable, we want the diagonal $a_P$ to be larger than the sum of the neighbors: $|a_P| > |a_W| + |a_E|$.

In Central Differencing (CDS), look at $a_E = C - D$. If the velocity $u^\circ$ is very high, $C$ becomes much larger than $D$. This can make the neighbor coefficients positive or negative in a way that destroys the diagonal dominance of the matrix.

This is exactly why high-speed (high Reynolds number) flows often crash when using Central Differencing—the "coefficients depending on $u$" make the matrix mathematically "unhealthy."

## Summary of the "Coefficient Loop":

- $u$ unknown: The value we are solving for in the current iteration.
- $u^\circ$ known: The "frozen" velocity used to calculate how much mass is flowing across faces.


# References

1. [珐式的CFD笔记 01：SIMPLE算法（1）压力速度耦合的计算难点](https://zhuanlan.zhihu.com/p/384557517)
2. [珐式的CFD笔记 02：SIMPLE算法（2）交错网格](https://zhuanlan.zhihu.com/p/385224499)
3. [珐式的CFD笔记 03：SIMPLE算法（3）稳态问题交错网格的SIMPLE算法](https://zhuanlan.zhihu.com/p/386201003)
4. [5 交错网格与SIMPLE算法（末尾有彩蛋）](https://www.cnblogs.com/yukibvvd/p/18636761/5-cross-grid-and-simple-algorithm-2xwxj)
5. [第6章 回流问题流动－传热 耦合计算的数值方法](http://staff.ustc.edu.cn/~humaobin/course/cht/ppt/6.0.pdf)
6. [6.4 原始变量顺序求解流场的 压力修正方法](http://staff.ustc.edu.cn/~humaobin/course/cht/ppt/6.4.pdf)
7. [OpenFOAM编程案例 | 14 SIMPLE算法](https://mp.weixin.qq.com/s/vEoW0lTb5QbfTFs7FZwGQQ)
8. [[CFD] The SIMPLE Algorithm (to solve incompressible Navier-Stokes)](https://www.youtube.com/watch?v=OOILoJ1zuiw&ab_channel=FluidMechanics101)
9. [什么是SIMPLE及PISO算法？](https://zhuanlan.zhihu.com/p/126368311?utm_psn=1845836334389919744)
10. [SIMPLE algorithm description](https://www.cfdsupport.com/OpenFOAM-Training-by-CFD-Support/node199.html)