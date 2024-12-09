---
layout: post
title: Maths | PDE | Overview
categories: [Maths]
tags: [NWP, AI, ML, PDE]
author: wpsze
date: 2024-10-25 11:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://upload.wikimedia.org/wikipedia/commons/thumb/2/2d/Partial_func_eg.svg/1280px-Partial_func_eg.svg.png
banner_img: https://upload.wikimedia.org/wikipedia/commons/thumb/2/2d/Partial_func_eg.svg/1280px-Partial_func_eg.svg.png
---

In mathematics, a **partial differential equation (PDE)** is an equation which computes a function between various partial derivatives of a multivariable function.

# Classification

A PDE is called **linear** if it is linear in the unknown and its derivatives. For example, for a function $u$ of $x$ and $y$, a second order linear PDE is of the form

$$
\begin{align*} 
a_1(x,y)u_{xx} + a_2(x,y)u_{xy} + a_3(x,y)u_{yx} + a_4(x,y)u_{yy} + a_5(x,y)u_x + a_6(x,y)u_y + a_7(x,y)u = f(x,y)
\end{align*} 
$$

where $a_i$ and $f$ are functions of the independent variables $x$ and $y$ only. (Often the mixed-partial derivatives $u_{xy}$ and $u_{yx}$ will be equated, but this is not required for the discussion of linearity.) 

- If the $a_i$ are constants (independent of $x$ and $y$) then the PDE is called **linear** with constant coefficients. 
- If $f$ is zero everywhere then the linear PDE is **homogeneous**, otherwise it is **inhomogeneous**. (This is separate from asymptotic homogenization, which studies the effects of high-frequency oscillations in the coefficients upon solutions to PDEs.

Nearest to linear PDEs are **semi-linear PDEs**, where only the highest order derivatives appear as linear terms, with coefficients that are functions of the independent variables. The lower order derivatives and the unknown function may appear arbitrarily. For example, a general second order semi-linear PDE in two variables is

$$
\begin{align*} 
a_1(x,y)u_{xx} + a_2(x,y)u_{xy} + a_3(x,y)u_{yx} + a_4(x,y)u_{yy} + f(u_x, u_y, u, x, y) = 0
\end{align*} 
$$

In a **quasilinear PDE** the highest order derivatives likewise appear only as linear terms, but with coefficients possibly functions of the unknown and lower-order derivatives:

$$
\begin{align*} 
a_1(u_x, u_y, u, x, y)u_{xx} + a_2(u_x, u_y, u, x, y)u_{xy} + a_3(u_x, u_y, u, x, y)u_{yx} + a_4(u_x, u_y, u, x, y)u_{yy} + f(u_x, u_y, u, x, y) = 0
\end{align*} 
$$

Many of the fundamental PDEs **in physics are quasilinear**, such as the **Einstein equations** of general relativity and the **Navier–Stokes equations** describing fluid motion.

A PDE without any linearity properties is called **fully nonlinear**, and possesses nonlinearities on one or more of the highest-order derivatives. An example is the **Monge–Ampère equation**, which arises in differential geometry.

# Second order PDE Classification

- [wiki](https://en.wikipedia.org/wiki/Partial_differential_equation#Second_order_equations)
- [Levandosky, Julie. "Classification of Second-Order Equations"](https://web.stanford.edu/class/math220a/handouts/secondorder.pdf)
- [Classification of PDEs](https://www.fluid.tuwien.ac.at/322042?action=AttachFile&do=get&target=Classification_v2.pdf)

The **elliptic/parabolic/hyperbolic classification** provides a guide to appropriate initial- and boundary conditions and to the smoothness of the solutions. Assuming $u_{xy} = u_{yx}$, the general linear second-order PDE in two independent variables has the form

$$
\begin{align*} 
Au_{xx} + Bu_{xy} + Cu_{yy} + Du_x + Eu_y + Fu +G= 0    
\end{align*} 
\tag{1}
$$
where $A,B,C,D,E,F,G$ may be functions of $x,y$. Caution: $A$ and $C$ must be non-zero for this method to work! If this is not the case, then use the generic method for second-order PDEs.

| Type       | Discriminant     | is reducible to            | Example            | Example equation      |
|------------|------------------|----------------------------|--------------------|-----------------------|
| `Elliptic`   | $B^2 - 4 AC < 0$ | $u_{xx} + u_{yy} + ...= 0$ | Laplace's equation | $u_{xx} + u_{yy} = 0$ |
| `Parabolic`  | $B^2 - 4 AC = 0$ | $u_{xx} + ...= 0$          | Heat equation      | $u_t - u_{xx} = 0$    |
| `Hyperbolic` | $B^2 - 4 AC > 0$ | $u_{xx} - u_{yy} + ...= 0$ | Wave equation      | $u_{tt} - u_{xx} = 0$ |

The three equations in Example above are of particular interest not only because they are derived from physical principles, but also because **every second-order linear equation of the form (1) can be reduced to an equation of one of those forms (plus lower-order terms) by making a change of variables**.

From the numerical point of view,

|                        |                 | Types                   | Computational Concern |
|------------------------|-----------------|-------------------------|-----------------------|
| Initial Value Problem  | Time evolution  | Parabolic or Hyperbolic | **Stability**             |
| Boundary Value Problem | Static solution | Elliptic                | **Efficiency**            |

## **Navier-Stokes equations (NS equations)**

The **Navier-Stokes equations (NS equations)**, which describe the motion of fluid substances (like liquids and gases), are a set of nonlinear partial differential equations (PDEs). Their type depends on how they're applied and simplified for specific contexts. Here’s a breakdown:

### 1. **Classification Based on Context**

PDEs are classified as **elliptic**, **parabolic**, or **hyperbolic**, depending on the system's properties. The Navier-Stokes equations can exhibit any of these behaviors depending on the conditions.

#### a) **Parabolic Type** (Diffusion-Dominated)

- When viscous (diffusive) terms dominate over inertial terms ($(\mathbf{u} \cdot \nabla) \mathbf{u}$) in low Reynolds number ($Re$) flows.
- Example: Slow, creeping flows like those governed by the **Stokes equation** (a simplification of Navier-Stokes for very low $Re$).

#### b) **Elliptic Type** (Steady-State Solutions)

- For steady flows ($\partial \mathbf{u} / \partial t = 0$), the Navier-Stokes equations can become elliptic.
- Pressure fields are determined elliptically in incompressible flows due to the coupling of velocity and pressure.

#### c) **Hyperbolic Type** (Advection-Dominated) or compressible regimes

- For high Reynolds number flows, where inertial terms dominate viscous terms, the flow becomes advection-dominated.
- Inviscid (frictionless) Navier-Stokes equations reduce to the **Euler equations**, which are hyperbolic.


## Properties of different types of PDEs

- `Elliptic`
  - Boundary conditions must be prescribed all along the boundary
  - **Local change of the boundary data influences the solution everywhere**
  - **No propagation of waves**
  - No real characteristics
- `Parabolic`
  - Parabolic equations arise by adding time dependence $u_t$ to equation which was originally elliptic.
  - Waves can propagate in one specific direction only, corresponding to time-like variable
  - Equivalent system of $n$ first-order equations has $1$ to $n-1$ real characteristics
  - Boundary conditions are required in space, initial condition in time
  - **Local change in boundary conditions affects immediately the solution at all positions in space, but only in future times**
- `Hyperbolic`
  - Waves can reflect from boundaries
  - Equivalent system of $n$ first-order equations has $n$ real characteristics.
  - Boundary conditions are required in space, initial condition in time
  - **Information about local change of boundary condition travels through space with finite speed.**

### Local Changes and Their Effects

Local modifications to boundary data can influence solutions in several ways, depending on the type of PDE:

- `Elliptic PDEs`, such as Laplace's equation, are characterized by their boundary conditions being prescribed over the entire boundary of the domain. The effects of local changes in boundary conditions on elliptic equations are profound:
  - **Immediate Influence**: A local change in boundary data will affect the solution throughout the entire domain immediately. This is due to the smoothing properties of elliptic equations, where solutions tend to be continuous and smooth across the domain. For instance, if a Dirichlet boundary condition is altered at one point, the solution will adjust in a way that reflects this change throughout the entire region.
  - **No Propagation Delay**: Unlike hyperbolic equations, there is no wave propagation; thus, changes do not require time to influence distant points in the domain. Instead, they affect all points instantaneously.
- `Parabolic PDEs`, such as the heat equation, exhibit different characteristics regarding local changes in boundary conditions:
  - **Temporal Influence**: Changes to boundary conditions will affect the solution at all spatial locations but only at future times. This means that while a local alteration might not immediately impact distant points, over time, these changes will propagate through the domain.
  - **Initial Conditions Matter**: In parabolic problems, both initial conditions and boundary conditions are crucial. A local change can lead to an evolution of the solution that reflects this change as time progresses. For example, if the temperature at a boundary is increased, it will eventually influence the temperature distribution throughout the domain as heat diffuses.
- `Hyperbolic PDEs`, such as wave equations, have unique properties concerning local changes:
  - **Directional Propagation**: Local changes in boundary conditions can influence solutions through wave propagation. The effects of such changes are constrained by the speed of waves; thus, alterations at one boundary may take time to reach other parts of the domain depending on their distance and direction.
  - **Reflection and Interaction**: Waves can reflect off boundaries, which means that local changes can create complex interactions within the domain as waves bounce back and forth. This can lead to intricate patterns in the solution that depend on both initial and boundary conditions.

{% note danger %}
The effects of local changes in boundary data on PDE solutions vary significantly across different types of equations. 

- Elliptic equations respond instantaneously throughout the domain, 
- Parabolic equations show temporal propagation effects, and 
- Hyperbolic equations exhibit directional influence based on wave behavior. 

Understanding these dynamics is essential for accurately modeling physical phenomena described by PDEs and for ensuring stability and reliability in numerical simulations.
{% endnote %}

1. [Information propagation rate in 2nd-order PDEs](https://math.stackexchange.com/questions/3937288/information-propagation-rate-in-2nd-order-pdes)
   - Propagation of information generally refers to how local changes in the initial/boundary data is reflected in the corresponding solution.
   - In the elliptic and parabolic cases, local changes to initial/boundary data can change the solution at every point, which is what we mean by infinite speed of propagation.
   - For wave equations this is not the case however, and changes to initial data is propagates along a 'wave cone.'
   - Why would some systems result in finite information propagation rate and others necessarily have infinite information propagation rate?

### Information Propagation in the Heat Equation

The heat equation, a fundamental parabolic partial differential equation, describes how heat diffuses through a medium over time. Its mathematical formulation is typically given as:

$$
\frac{\partial T}{\partial t} = \alpha \frac{\partial^2 T}{\partial x^2}
$$

where $ T(x, t) $ is the temperature at position $ x $ and time $ t $, and $ \alpha $ is the thermal diffusivity constant. Understanding how information propagates in this context is key to grasping the equation's implications in physical scenarios.

#### **Key Characteristics of Information Propagation**

1. **Infinite Speed of Propagation**:
   - Unlike hyperbolic equations (e.g., the wave equation), which have a finite speed of propagation, the heat equation exhibits an **infinite speed of propagation**. This means that changes in temperature at any point in the medium affect all other points instantaneously. For example, if a sudden change occurs at one point, the entire domain feels this change immediately, leading to a smoothing effect across the temperature distribution.
     - Intuitively, **Brownian motion** has infinite propagation speed, as it has a **non-zero probability of reaching any point in any arbitrarily short time**. This is due to the fact that the probability density of the normal distribution is strictly greater than zero over the entire space.

2. **Smoothing Effect**:
   - The heat equation inherently smooths out temperature distributions over time. Initially sharp changes or discontinuities in temperature will become less pronounced as time progresses. This behavior can be observed in the solution to the heat equation, where initial conditions are represented by a Dirac delta function, leading to solutions that spread and flatten out over time. 

3. **Loss of Information**:
   - As time progresses, information about the initial temperature distribution gradually dissipates. The solution approaches a steady state where temperature differences diminish, reflecting a loss of information about the initial conditions. This contrasts with hyperbolic equations where information is preserved along characteristic lines.

4. **Mathematical Representation**:
   - The fundamental solution (or heat kernel) for the heat equation illustrates these properties mathematically. In one dimension, it can be expressed as:

   $$
   S(x, t) = \frac{1}{\sqrt{4\pi \alpha t}} e^{-\frac{x^2}{4\alpha t}}
   $$

   This solution shows that as $ t $ increases, the peak of the Gaussian becomes flatter and approaches zero for fixed $ x $, indicating that localized heat effects spread out and diminish over time.

#### **Implications of Infinite Propagation Speed**

The notion of infinite speed of propagation can be counterintuitive:

- **Physical Interpretation**: While it might seem that changes propagate instantaneously, this does not imply actual physical movement at infinite speeds. Instead, it reflects how thermal energy diffuses uniformly across the medium due to local interactions at the molecular level.
- **Practical Considerations**: In real-world applications, such as heat conduction in solids or fluids, this model simplifies analysis but may not account for delays due to physical constraints or material properties.

#### **Conclusion**

The heat equation's characteristics—specifically its infinite speed of propagation and smoothing effect—highlight its unique role among PDEs. Understanding these properties is crucial for modeling thermal processes accurately and recognizing how initial conditions influence long-term behavior in systems governed by diffusion phenomena.

### ChatGPT 

The statement "heat equation exhibits infinite speed of propagation" refers to a mathematical property of the classical **heat equation**, which describes how heat diffuses through a medium over time. The heat equation is typically written as:

$$
\frac{\partial u}{\partial t} = \alpha \nabla^2 u
$$

where:

- $ u = u(x,t) $ is the temperature distribution,
- $\alpha > 0$ is the thermal diffusivity,
- $\nabla^2$ is the Laplacian operator.

#### Meaning of Infinite Speed of Propagation
In the heat equation, even if the initial temperature distribution $u(x,0)$ has compact support (nonzero only in a finite region of space), the solution $u(x,t)$ for $t > 0$ becomes nonzero everywhere, even arbitrarily far from the initial region. This means that information about the temperature change "propagates" instantaneously to all parts of the medium, albeit with exponentially small magnitude at large distances.

#### Why Does This Happen?
This property arises from the mathematical nature of the heat equation:

1. The heat equation is a **parabolic partial differential equation**.
2. Parabolic equations do not have a finite propagation speed for disturbances because they involve second-order spatial derivatives without any mechanism limiting the spread of effects.

#### Physical Implications
In real-world systems, the infinite speed of propagation predicted by the classical heat equation is not physically realistic because no physical process can propagate at infinite speed. **The discrepancy arises because the heat equation is a continuum approximation and does not account for physical constraints such as the finite speed of thermal energy transport at the atomic or molecular level**.

#### Modified Theories
To address this issue, **hyperbolic heat equations** or **Cattaneo's equation** are sometimes used. These models incorporate a finite speed of propagation by introducing a relaxation time for heat flux. For example, Cattaneo's equation modifies the heat equation to include a delay effect:

$$
\tau \frac{\partial q}{\partial t} + q = -k \nabla u
$$

where $\tau$ is the relaxation time, $q$ is the heat flux, and $k$ is the thermal conductivity. This modification ensures finite propagation speed.

#### If heat equation exhibits infinite speed of propagation , how can we apply finite difference method?

Applying the finite difference method (FDM) to the heat equation is possible and commonly done, even though the equation exhibits infinite speed of propagation. This works because the infinite speed of propagation is a mathematical idealization that does not directly affect numerical methods. Here's why FDM can still be applied effectively:

#### Key Considerations for FDM and Infinite Speed of Propagation

1. **Locality of Computation**:
   - In FDM, the temperature at a grid point is updated based on values from neighboring points within a small finite region (e.g., using a stencil in space and time).
   - Although the theoretical solution predicts nonzero values everywhere, these values are often exponentially small and do not significantly affect computation beyond a finite domain.

2. **Truncation of the Domain**:
   - In practice, computations are performed on a finite spatial domain, often with boundary conditions applied to simulate the effects of the surrounding infinite domain.
   - Any influence from distant points outside the computational domain is negligible due to the rapid decay of the solution in space.

3. **Temporal Resolution**:
   - The FDM uses small time steps to advance the solution, which aligns with the localized propagation of numerical updates.
   - Even though theoretically, information propagates instantaneously, the numerical scheme respects the finite time step, ensuring stable and accurate progression.

4. **Stability and Convergence**:
   - Stability conditions, such as the **CFL condition** (Courant-Friedrichs-Lewy), are enforced to ensure that the numerical solution behaves well.
   - For the explicit FDM for the heat equation, the time step size $\Delta t$ and spatial grid size $\Delta x$ must satisfy the condition:
     $$
     \Delta t \leq \frac{\Delta x^2}{2\alpha}
     $$
     This condition limits the temporal and spatial resolution, mitigating issues from infinite propagation speed.

5. **Practical Approximations**:
   - In numerical simulations, small initial perturbations may diffuse quickly, but their contributions are bounded by the numerical resolution and truncation errors.
   - The numerical approximation only captures behavior within the computational grid and time resolution, effectively ignoring the infinite speed aspect.

#### How FDM Works in Practice

To solve the 1D heat equation:

$$
\frac{\partial u}{\partial t} = \alpha \frac{\partial^2 u}{\partial x^2},
$$

we discretize it using finite difference approximations:

- **Time derivative**: $\frac{\partial u}{\partial t} \approx \frac{u_i^{n+1} - u_i^n}{\Delta t}$,
- **Second spatial derivative**: $\frac{\partial^2 u}{\partial x^2} \approx \frac{u_{i+1}^n - 2u_i^n + u_{i-1}^n}{\Delta x^2}$.

Substituting these into the equation gives:

$$
\frac{u_i^{n+1} - u_i^n}{\Delta t} = \alpha \frac{u_{i+1}^n - 2u_i^n + u_{i-1}^n}{\Delta x^2}.
$$

Rearranging, we obtain the update rule:

$$
u_i^{n+1} = u_i^n + \frac{\alpha \Delta t}{\Delta x^2} (u_{i+1}^n - 2u_i^n + u_{i-1}^n).
$$

#### Handling Infinite Propagation Numerically
- The numerical scheme is inherently local, updating $u_i^{n+1}$ based only on immediate neighbors ($u_{i-1}^n$, $u_i^n$, $u_{i+1}^n$).
- The effects of distant changes are limited by the numerical grid and do not cause instability or spurious results if the scheme is stable.

#### Conclusion
The infinite speed of propagation in the heat equation is a theoretical concern that does not prevent FDM from being applied. Numerical schemes operate on a discretized version of the problem, which respects finite grid and time step sizes. With appropriate domain size, boundary conditions, and stability checks, FDM provides accurate solutions for practical problems involving the heat equation.

## Generalizing to Higher Dimensions

If there are n independent variables x1, x2 , …, xn, a general linear partial differential equation of second order has the form,

$$
\begin{align*} 
L u =\sum_{i=1}^n\sum_{j=1}^n a_{i,j} \frac{\partial^2 u}{\partial x_i \partial x_j} \quad+ \text{lower-order terms} = 0
\end{align*} 
$$

The classification depends upon the signature of the eigenvalues of the coefficient matrix $A = a_{i,j}$.

- `Elliptic`: the eigenvalues are all positive or all negative.
- `Parabolic`: the eigenvalues are all positive or all negative, except one that is zero.
- `Hyperbolic`: there is only one negative eigenvalue and all the rest are positive, or there is only one positive eigenvalue and all the rest are negative.
- `Ultrahyperbolic`: there is more than one positive eigenvalue and more than one negative eigenvalue, and there are no zero eigenvalues.

However, the classification only depends on linearity of the second-order terms and is therefore applicable to semi- and quasilinear PDEs as well. 

# References

1. [Partial differential equations](https://www.ljll.fr/frey/cours/UdC/ma691/ma691_ch3.pdf)
2. [Classification of PDEs](http://www.fluid.tuwien.ac.at/322042?action=AttachFile&do=get&target=Classification_v2.pdf)
3. [Chapter 6 Partial Differential Equations](https://cns.gatech.edu/~predrag/courses/PHYS-6124-12/StGoChap6.pdf)
   1. domain of influence
   2. domain of dependence
4. [丘成桐院士演講: 偏微分方程的方法 (**推薦**)](https://web.math.sinica.edu.tw/media/pdf/d172/17201.pdf)
   1. 探討了偏微分方程在動態和靜態問題中的應用，特別是如何利用這些方程來理解系統的行為
   2. 引入了「極限環」的概念，描述當時間足夠長時，系統會趨近於某一穩定軌道
   3. 當系統慢慢接近這一軌道時，可以得到一個與時間無關的方程，這個方程在機率上僅與 $ f $ 本身有關
   4. 研究如**熱方程**和**波動方程**等工具的重要性，以便更好地理解 **Laplace 方程**及其解的整體行為
5. [The finite difference method](https://www.ljll.fr/frey/ftp/finite-differences.pdf)
   1. It is well-known that solving the initial-value problem for the heat equation forward in time takes a discontinuous initial temperature $u$ at time $t_0$ into a temperature which is instantly smooth as soon as the times $t > t_0$ . This may not be physically possible, since there would then be information propagation at infinite speed, which is in contradiction with the **causality** principle. Therefore this is a theoretical property of the mathematical equation.