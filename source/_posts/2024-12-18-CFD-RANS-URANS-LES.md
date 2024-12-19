---
layout: post
title: "CFD | RANS vs. URANS vs. LES"
categories: [CFD]
tags: [OpenFOAM, HPC]
author: wpsze
date: 2024-12-18 16:06:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/GkV6pMK.png
banner_img: https://i.imgur.com/GkV6pMK.png
---

# Characteristics of turbulence

One characteristic of turbulent flows is their **irregularity** (or **randomness**). A fully deterministic approach to characterize turbulent flows is very difficult. Turbulent flows are usually described statistically.

- The **diffusivity** of turbulence cause rapid mixing and increased rates of momentum, heat, and mass transfer. A flow that looks random but does not exhibit the spreading of velocity fluctuations through the surrounding fluid is not turbulent.
- Turbulent flows are **dissipative**. Kinetic energy gets converted into heat due to viscous shear stresses. Turbulent flows die out quickly when no energy is supplied.
- Turbulent flows always occur at **high Reynolds numbers**. They are caused by a complex interaction between the viscous forces and convection.
- Turbulent flows are rotational, that is, they have **non-zero vorticity**. Mechanisms such as the stretching of three-dimensional vortices play a key role in turbulence.
- Turbulence is a **continuum phenomenon**. Even the smallest eddies are significantly larger than the molecular scales.
- Turbulence is a **feature of flow and is not a property of the fluid**. A liquid or a gas at high Reynolds number will exhibit the same dynamics.

1. **Turbulent flows can originate at the walls**. When this is the case, we talk about `wall bounded turbulence`.
2. **Turbulent flows can also originate in the absence of walls (or far from walls)**. When this is the case, we talk about `shear free turbulence` (usually **jets, heated walls, atmospheric flows**).

# Instantaneous fluctuations – Removing small scales

We have seen that turbulent flows are characterize by instantaneous fluctuations of velocity, pressure, and all
transported quantities.

- In most engineering applications is not of interest resolving the instantaneous fluctuations.
- To avoid the need to resolve the instantaneous fluctuations (or small scales), two methods can be used:
  - **Reynolds averaging**.
  - **Filtering**.
- If you want to resolve all scales, you conduct DNS simulations, which are very computationally intensive.
- Two methods can be used to eliminate the need to resolve the small scales:
  - **Reynolds averaging (RANS/URANS)**:
    - All turbulence scales are modeled.
    - Can be 2D and 3D.
    - Can be steady or unsteady.
  - **Filtering (LES/DES)**:
    - Resolves large eddies.
    - Models small eddies.
    - Intrinsically 3D and unsteady.
- Both methods introduce additional terms in the governing equations that must be
modeled (these terms are related to the instantaneous fluctuations).
- The final goal of turbulence modeling is to find the closure equations to model these
additional terms (usually a stress tensor).

![](https://i.imgur.com/yMpYzXN.png){width=600}

![](https://i.imgur.com/SrU2JMy.png){width=600}

# Reynolds Averaging (RANS)

{% note primary %}
**Averaging the quantities (time average, spatial average or ensemble average)**
{% endnote %}

- Reynolds averaging simple consists in:
  - Splitting the instantaneous value of the primitive variables into a mean component and a fluctuating component (Reynolds decomposition).
  - **Averaging the quantities (time average, spatial average or ensemble average)**.
  - Applying a few averaging rules to simplify the equations
  - When we use Reynolds averaging, we are taking a statistical approach to turbulence
modeling.

1. **RANS (Reynolds-Averaged Navier-Stokes)**: Time averaging
2. **URANS (Unsteady Reynolds-Averaged Navier-Stokes)**: Ensemble averaging

![](https://i.imgur.com/hIMgzph.png){width=600}

## Time averaging
{% note primary %}
**Time averaging is appropriate for stationary turbulence or slowly varying turbulent flows.**
{% endnote %}

$$
\bar{\phi}(\mathbf{x})=\lim _{T \rightarrow \infty} \frac{1}{T} \int_t^{t+T} \phi(\mathbf{x}, t) d t
$$

## Spatial averaging
{% note primary %}
**Spatial averaging is appropriate for homogenous turbulence.**
{% endnote %}

$$
\bar{\phi}(t)=\lim _{V \rightarrow \infty} \frac{1}{V} \int_V \phi(\mathrm{x}, t) d V
$$

## Ensemble averaging 
{% note primary %}
**Ensemble averaging is appropriate for unsteady turbulence.**
{% endnote %}

- In ensemble averaging, **the number or realizations (or experiments) must be large enough** to eliminate the effects of fluctuations. This type of averaging can be used with steady or unsteady flows.
- If you can afford it, ensemble averaging is recommended. Have in mind that CFD is deterministic, **so you should start each realization using different initial conditions and boundary conditions fluctuations to obtain different outcomes**.

$$
\bar{\phi}(\mathrm{x}, t)=\lim _{ N \rightarrow \infty} \frac{1}{N} \sum_{i=1}^N \phi(\mathrm{x}, t)
$$

## Averaging rules

![](https://i.imgur.com/l3vVmDc.png){width=600}

## Question: URANS

- URANS we basically insert the time derivative and ensemble-average the flow for a certain time step (see the picture, which is taken from the book of Ferzinger, 2020; left side is supposed to be RANS and right side is supposed to be URANS).
- URANS is a Reynolds-Averaged equation. When you do this reynolds-averaging you assume that you average long enough so that you resolve the mean. That is, you assume you CAN actually find the average. If the flow time-scales and turbulent time-scales are similar and you don't average long enough the turbulent contribution will not be zero and you therefore get a mean-flow that is contaminated by turbulence statistics (which is not the true mean flow). This to me in a user-error and not a problem with URANS. If you don't sample long enough to get the correct statistics then you simply get the wrong statistics.[link](https://www.cfd-online.com/Forums/main/6519-rans-urans.html)
- I think the definition of URANS is contentious for CFD theorists and therefore so is its applicability. In general, URANS isn't really defined very well. I've seen some try to define it in a similar way to LES in terms of how the energy spectrum is captured/modelled. I think this discussion really becomes important (useful) when developing hybrid resolving/modelling method (DES). Moreover, most if not all the commercial codes do not explain the foundation of how their URANS approuch is implimented. I'm sure it's usually just ensuring there's a time derivative in the transport equations. But agree with other posts here, for all intents and purposes, transient RANS ~ URANS. [link](https://www.reddit.com/r/CFD/comments/k40bit/difference_between_unsteady_rans_urans_and/)
- 对于 $\int_t^{t+\Delta t}$ 的 $\Delta t$，从理论上 URANS 这个$\Delta t$是任意的，如果不考虑附加的湍流封闭模型如ke之类，$\Delta t$足够的小，就变成了LES (**LES can be applied spatial/temporal/both filtering**)，$\Delta t$足够的大，就变成了RANS。然而$\Delta t$ 并不需要显性的给定。所以对URANS取决定性的还是 $\mu_t$. [link](https://cfd-china.com/topic/6517/rans-urans-des-les-vles/13)
  - 类似的，URANS与LES从传输方程上区别也非常少，区别仅仅在与 $\nu$ 的模化，如果是LES的 $\nu$ ，那就是LES，如果是URANS的 $\nu$ ，那就是URANS。湍流理论会从底层特别深的地方，推导出来 $\nu$ 的不同，但是代码就是代码，不知道底层啥样的，到了代码这一层，只不过就是 $\nu$ 的大小不 同。
- **In below figure (right), let's define $T_1 << T << T_2$ for URANS.**
- **Question: What turbulence model does URANS use?** 1) Same as RANS?

![](https://i.imgur.com/yMpYzXN.png){width=600}

## Question: What is phase averaging?

# Large eddy simulation (LES) 

Large eddy simulation (LES) is a mathematical model for turbulence used in computational fluid dynamics. It was initially proposed in **1963 by Joseph Smagorinsky** to simulate atmospheric air currents, and **first explored by Deardorff (1970)**.

An LES filter can be applied to a spatial and temporal field $ \phi(\boldsymbol{x},t) $ and **perform a spatial filtering operation**, **a temporal filtering operation**, or **both**. The filtered field, denoted with a bar, is defined as

$$
\overline{\phi(\boldsymbol{x},t)} = \displaystyle{
\int_{-\infty}^{\infty}} \int_{-\infty}^{\infty} \phi(\boldsymbol{r},\tau) G(\boldsymbol{x}-\boldsymbol{r},t - \tau) d\tau d \boldsymbol{r}
$$

$$
\overline{\phi} = G \star \phi
$$

$$
\phi = \bar{\phi} + \phi^{\prime}
$$  

## Smagorinsky–Lilly model

The first SGS model developed was the Smagorinsky–Lilly SGS model, which was developed by **Joseph Smagorinsky** and used in the **first LES simulation by Deardorff**. It models the eddy viscosity as:

$$
\nu_\mathrm{t} 
= C \Delta^2\sqrt{2\bar{S}_{ij}\bar{S}_{ij}} 
= C \Delta^2 \left| \bar{S} \right|
$$

where $\Delta$ is the **grid size** and $C$ is a constant.

This method assumes that the energy production and dissipation of the small scales are in equilibrium.

# Ex: 1D Burgers equation

Consider the 1D Burgers equation in terms of the velocity field $u(x, t)$
$$
\begin{equation*}
\frac{\partial u}{\partial t}+\frac{\partial u^{2} / 2}{\partial x}=\frac{\partial}{\partial x}\left(v \frac{\partial u}{\partial x}\right) \tag{1}
\end{equation*}
$$

## LES

In the continuous **LES approach the scale separation is obtained via low-pass filtering**. The spatial filtering operation (no time-filtering is considered here) can be expressed as the convolution product between the unfiltered point-wise velocity field and some suitable filter function $G$, that is
$$
\begin{equation*}
\bar{u}(x, t ; \Delta)=\int_{R} G\left(x-x^{\prime} ; \Delta\right) u\left(x^{\prime}, t\right) d x^{\prime} \tag{2}
\end{equation*}
$$
$\Delta$ being the characteristic filter width. The LES equation is obtained by applying (2) to each term of (1)
$$
\begin{equation*}
\frac{\partial \bar{u}}{\partial t}+\frac{\partial \bar{u}^{2} / 2}{\partial x}=\frac{\partial}{\partial x}\left(v \frac{\partial \bar{u}}{\partial x}\right)+\underbrace{\frac{\partial\left(\bar{u}^{2}-u^{2}\right) / 2}{\partial x}}_{\frac{\partial f(\bar{u}, u)}{\partial x}} \tag{3}
\end{equation*}
$$

Commutation between filtering and derivative being applied

## URANS

As a counterpart, in the continuous **URANS approach a statistical averaging** is adopted.
$$
\begin{equation*}
\langle u\rangle(x, t)=\lim _{N \rightarrow \infty} \frac{1}{N} \sum_{k=1}^{N} u_{k}(x, t) \tag{4}
\end{equation*}
$$


$N$ being the **number of sample of the ensemble averaging**. The URANS equation is obtained by applying (4) to each term of (1)

$$
\begin{equation*}
\frac{\partial\langle u\rangle}{\partial t}+\frac{\partial\langle u\rangle^{2} / 2}{\partial x}=\frac{\partial}{\partial x}\left(v \frac{\partial\langle u\rangle}{\partial x}\right)+\underbrace{\frac{\partial\left(\langle u\rangle^{2}-u^{2}\right) / 2}{\partial x}}_{\frac{\partial g(\langle u\rangle, u)}{\partial x}} \tag{5}
\end{equation*}
$$

## Close equations

In order to close Eqs.(3) and (5), suitable models are used, that is 

$$
f(\bar{u}, u) \cong f_{\text {LES }}(\bar{u}), \qquad g(\langle u\rangle, u) \cong g_{\text {URANS }}(\langle u\rangle)
$$

## Discretization

If we consider now the practical solution, a discretization of the derivative is introduced. Assume the same discretization is adopted for the LES and URANS equations, so that we can write a unique equation
$$
\frac{\delta u_{j}}{\delta t}+\frac{\delta u_{j}^{2} / 2}{\delta x}=\frac{\delta}{\delta x}\left(v \frac{\delta u_{j}}{\delta x}\right)+\frac{\delta}{\delta x}\left\{\begin{array}{c}
f_{L E S}\left(u_{j}\right)  \tag{6}\\
g_{\text {URANS }}\left(u_{j}\right)
\end{array}\right.
$$
that characterizes LES or URANS depending on the closure model.

{% note primary %}
One can deduce that the discrete equation (6) never implied a real application of a spatial filtering or an ensemble averaging. **The only assessment is the spatial filtering implicitly induced by the discretization, a fact that is congruent to the LES assumption but not to the URANS one**.
{% endnote %}

The Eq.(6) must be well posed by the set of initial and boundary conditions. This point adds a distinction as they should be different for LES and URANS.

# References

1. [Joel Guerrero's homepage](http://www.dicat.unige.it/guerrero/teaching.html)
   1. [2tur_preliminaries (**recommend**)](http://www.dicat.unige.it/guerrero/turbulence2020/slides/2tur_preliminaries.pdf)
   2. [3turbulence_Scales_law_of_the_wall_wall_function (**recommend**)](http://www.dicat.unige.it/guerrero/turbulence2020/slides/3turbulence_Scales_law_of_the_wall_wall_function.pdf)
   3. [5governing_equations (**recommend**)](http://www.dicat.unige.it/guerrero/turbulence2020/slides/5governing_equations.pdf)
2. [turbulence_2021_OF8 (**recommend**)](https://www.wolfdynamics.com/training/turbulence/OF2021/turbulence_2021_OF8.pdf)
3. [URANS vs. LES](https://www.researchgate.net/profile/Filippo-Maria-Denaro/post/URANS-what-is-the-meaning-for-statistically-steady-flows-and-what-compared-to-LES/attachment/5b407d1e4cde265cb64d663d/AS%3A645677793165312%401530952990388/download/URANS+vs.+LES.pdf)