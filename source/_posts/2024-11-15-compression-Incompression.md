---
layout: post
title: CFD | Incompressible and Compressible
categories: [CFD]
tags: []
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2024-11-15 10:32:00
index_img: https://i.imgur.com/rDx2DHd.png
banner_img: https://i.imgur.com/rDx2DHd.png
---

# Governing Equations

The equations that govern the fluid flow are based on the conservation laws of a fluid’s physical properties. The basic equations are the three laws of conservation1:

- Conservation of Mass: **Continuity Equation**
- Conservation of Momentum: **Navier Stokes Equation**
- **Equation of state**
- Conservation of Energy: First Law of Thermodynamics or **Energy Equation**
  - An additional enthalpy equation from thermodynamics is required that includes a dissipation term and accounts for temperature changes during flow when heat or any other energy exchange is involved.Convective and conjugate heat transfer simulations involve thermal energy transfers for accurate CFD analysis. Stress-strain relations for compressible fluids can further complicate equations of compressible flow.

## Equations

Conservation of Mass and Momentum, 
$$
\begin{align*}
\frac{D(\,)}{Dt} &= \frac{\partial(\,)}{\partial t}+V\cdot\nabla(\,) \tag{0} \\
\dfrac{D \rho}{D t} + \rho (\nabla \cdot \vec{v}) &= 0 \tag{1}\\
\rho \dfrac{D\vec{v}}{D t} &= -\nabla p + \mu \nabla^2 \vec{v} + \rho \vec{g}  \tag{2}\\
\end{align*}
$$

Equation of state,
$$
\begin{align*}
P &= P(\rho, T) \qquad (e.g. PV = nRT) \tag{3}\\
\end{align*}
$$

Energy Equation,
$$
\begin{align*}
dE_t & = dQ + dW \tag{4}\\
\end{align*}
$$

where $dQ$ is the heat added to the system, $dW$ is the work done on the system, and $dE_t$ is the increment in the total energy of the system. One of the common types of energy equation is:

$$
\begin{align*}
\displaystyle \dfrac{D{\cal E}}{Dt} &= - \frac{p}{\rho}\,\nabla\cdot{\bf v} + \frac{\chi}{\rho} +\frac{\nabla\!\cdot( \kappa\,\nabla T)}{\rho} \tag{5} \\
\displaystyle \chi &= \frac{\partial v_i}{\partial x_j} d_{ij} = 2 \mu \left(e_{ij} e_{ij} - \dfrac{1}{3} e_{ii} e_{jj} \right) = \mu \left( \dfrac{\partial v_i}{\partial x_j}\dfrac{\partial v_i}{\partial x_j} + \dfrac{\partial v_i}{\partial x_j}\dfrac{\partial v_j}{\partial x_i} - \dfrac{2}{3} \dfrac{\partial v_i}{\partial x_i}\dfrac{\partial v_j}{\partial x_j} \right)
\end{align*}
$$

According to the previous equation, the internal energy per unit mass of a co-moving fluid element evolves in time as a consequence of work done on the element by pressure as its volume changes, viscous heat generation due to flow shear, and heat conduction.

# Incompressible flow

In most situations of general interest, the flow of a conventional liquid, such as water, is incompressible to a high degree of accuracy. A fluid is said to be incompressible when the mass density of a co-moving volume element does not change appreciably as the element moves through regions of varying pressure. In other words, **for an incompressible fluid, the rate of change of $ \rho$ following the motion is zero**: that is,

$$
\displaystyle \frac{D\rho}{Dt} = 0
$$

In this case, the continuity equation reduces to
$$
\displaystyle \nabla\cdot{\bf v} = 0
$$

We conclude that, as a **consequence of mass conservation**, 

{% note primary %}
An **incompressible fluid must have a divergence-free**, or solenoidal, velocity field. 
{% endnote %}

This immediately implies that **the volume of a co-moving fluid element is a constant of the motion**. In most practical situations, the initial density distribution in an incompressible fluid is uniform in space. Hence, it follows from Equation (1.76) that the density distribution remains uniform in space and constant in time. In other words, we can generally treat the density, $ \rho$ , as a uniform constant in incompressible fluid flow problems.

![](https://i.imgur.com/XwL79J8.png){width=600}
![](https://i.imgur.com/zaf58vA.png){width=600}
![](https://i.imgur.com/GkV6pMK.png){width=600}
![](https://i.imgur.com/6FStEBC.png){width=600}
![](https://i.imgur.com/iznmeSr.png){width=600}
![](https://i.imgur.com/RjdjunO.png){width=600}

# Compressible flow

A compressible flow is a flow in which the fluid density $\rho$ varies significantly within the flowfield. Therefore, $\rho(x, y, z)$ must now be treated as a field variable rather than simply a constant. Typically, significant density variations start to appear when the flow Mach number exceeds 0.3 or so. The effects become especially large when the Mach number approaches and exceeds unity.

In incompressible flow the density $\rho$ does not change.

![](https://i.imgur.com/rDx2DHd.png){width=600}

A perfect gas is one whose individual molecules interact only via direct collisions, with no other intermolecular forces present. For such a perfect gas, the properties $P, V, \rho$, and the temperature $T$ are related by the following **equation of state**,

$$
PV = nRT
$$

where $R$ is the specific gas constant. For air, $R = 287 J⋅kg^{−1}⋅K $.

The appearance of the temperature $T$ in the equation of state means that it must vary within the flowfield. Therefore, $T(x, y, z)$ must be treated as a new field variable in addition to $\rho(x, y, z)$. n the moving  Control Volume (CV) scenario above, the change in the CV’s volume is not only accompanied by a change in density, but by a change in temperature as well.

The appearance of the temperature also means that thermodynamics will need to be addressed. So in addition to the **conservation of mass and momentum** which were employed in low speed flows, we will now also need to consider the **conservation of energy**. The following table compares the variables and equations which come into play in the two cases.

![](https://i.imgur.com/qeDj31C.png){width=600}

## Mach number 馬赫數

儘管氣體是可壓縮的，但在低速時它們所經歷的密度變化可能並不顯著。以空氣為例。圖顯示了以馬赫數為函數的密度變化圖。密度變化表示為 $\rho/\rho_0$，其中 $\rho_0$ 是零速度（即，零馬赫數）時的空氣密度。

![以空氣為例。圖顯示了以馬赫數為函數的密度變化圖](https://i.imgur.com/dND1Epn.png){width=400}

{% note primary %}
對於**不可壓縮流動，溫度通常保持恆定**。但在**可壓縮流動中，可能會發生顯著的溫度變化，導致能量模式之間的交換** (在計算可壓縮流動時，**必須考慮能量方程式**)
{% endnote %}

對於馬赫數高達0.3的情況，密度變化在 $\rho_0$ 的5%以內。因此，在這個範圍內，實際上可以忽略密度變化，並將流動視為不可壓縮的。但是，當馬赫數超過0.3時，變化確實變得顯著，並且在馬赫數為1時，密度變化達到了36.5%。在馬赫數為2時，密度變化高達77%。不可壓縮流和可壓縮流之間的另一個重要區別是由於溫度變化引起的。對於**不可壓縮流動，溫度通常保持恆定**。但在**可壓縮流動中，可能會發生顯著的溫度變化，導致能量模式之間的交換**。這些事實的直接後果是，在計算可壓縮流動時，**必須考慮能量方程式**（這在不可壓縮流動中沒有進行）。此外，為了處理能量模式的交換，就必須理解流動的熱力學。熱力學是一個涵蓋許多主題的龐大主題。

## [openFOAM案例：可壓縮流體模擬](https://mp.weixin.qq.com/s/Z0K06ziGHhbbhVNLpTv2cg)

- 可壓縮流體的求解要點

物體在可接近音速或超音速飛行的時候，由於速度非常快，空氣就會因為運動被壓縮，它的密度就會改變，也變成了可壓縮流體。與不可壓縮流體相比，可壓縮流體在CFD模擬上有些差異，首先我們來看控制方程式：

![](https://i.imgur.com/HL8RfHp.png){width=400}

{% note primary %}
可壓縮流體的控制方程式和我們之前遇到的方程式相比，**最大的特性就是它的密度就不再是常數**了。既然密度成為了一個變量，**為了讓方程組封閉。我們就需要一個方程式對密度的變化來描述**，而這個方程式就是**狀態方程式**。狀態方程式就是物質密度隨溫度壓力變化的關係。那麼由於狀態方程式裡又牽涉到溫度了。**描述溫度就需要一個能量方程式**。所以我們求解可壓縮流體都是要同時求解能量方程式。
{% endnote %}

求解可壓縮流體的方程，最簡單的一個思路就是採用和先前提到的**不可壓縮流體演算法類似**的方法來求解。例如之前提到的 `simple` 或 `piso` 演算法，先解**動量方程得**到 U，再將動量方程與壓力方程式變換，再得到**壓力方程**，得到 P。再透過壓力**修正方程式校正U**，不斷迭代得到求解。與不可壓縮流體相比，可壓縮流體的密度是可變的，我們透過狀態方程式引入這種變化就可以了。**這裡速度與壓力的求解是分開的所以叫分離式求解**，而且推導出來的是壓力方程，因此叫**基於壓力的求解**。

![](https://i.imgur.com/3s6rOru.png){width=600}

還有一種方法是**基於密度的求解**，**把狀態方程帶入動量方程，把壓力項變成密度項**。這樣就可以直接把連續性方程式與動量方程式連結起來同時求解。所以這就叫**耦合求解，求解出來的是速度場和密度場**，所以又叫基於密度的求解。把速度場與密度場求出來以後，**再用狀態方程式把壓力場求出來**就可以了。

![](https://i.imgur.com/fuChsqX.png){width=600}

**openfoam** 裡，基於密度的解算器是 `rhoCentralFoam` ，基於壓力的解算器是 `rhoPimpleFoam` 。從數學角度而言兩種解算器原理上都可以求解可壓縮流體流動，但由於演算法與離散格式上的區別，二者的使用範圍稍有不同。兩種解算器基於壓力的解算器，更接近不可壓縮流體的解方式，適宜低馬赫數的流體。基於密度的求解器，適用於高馬赫數的流體。現在隨著演算法與離散方式的進步，二者解馬赫數的範圍都在擴大。

# References

1. [Fluids – Lecture 11 Notes](https://web.mit.edu/16.unified/www/SPRING/fluids/Spring2008/LectureNotes/f11.pdf)
2. [13.024-Numerical Methods in Incompressible Fluid Mechanics](https://ocw.mit.edu/courses/2-29-numerical-marine-hydrodynamics-13-024-spring-2003/01e395bcab50dc4cd8f82ebaa045aca3_lecture_notes.pdf)
3. [Convective Time Derivative](https://farside.ph.utexas.edu/teaching/336L/Fluidhtml/node11.html)
4. [Energy Conservation](https://farside.ph.utexas.edu/teaching/336L/Fluidhtml/node14.html)
5. [Equations of Incompressible Fluid Flow](https://farside.ph.utexas.edu/teaching/336L/Fluidhtml/node15.html)
6. [Compressible Flow vs Incompressible Flow](https://www.simscale.com/docs/simwiki/cfd-computational-fluid-dynamics/compressible-flow-vs-incompressible-flow/)
7. [针对昨天压力悖论的解释](https://mp.weixin.qq.com/s/_OwJGqOovMjfy8ax42ANOA)
   1. 为什么我们会陷入这个悖论？我想很多做不可压缩流体模拟的朋友，平时常用的就四组方程：一个连续性方程，三个动量方程。连续性方程里面有一个密度和三个速度，带来四个未知量；三个动量方程带来一个新的未知量——压力。四个方程，五个未知量，怎么求解？所以我们通常会给定密度。这样方程组就封闭了。但是昨天我跟你说，要考虑状态方程，这就多了一个方程，但是你还是认为没有多出任何未知量（温度、密度都给定）。这样方程组就超定了，于是产生矛盾。实际上，加入状态方程后，密度就不能再认为恒定了。所以这可能是一个思维定势的坑。
8. [openFOAM案例：可压缩流体模拟](https://mp.weixin.qq.com/s/Z0K06ziGHhbbhVNLpTv2cg)

