---
layout: post
title: CFD | Flat Plate
categories: [CFD]
tags: [OpenFOAM, HPC, Blasius Solution]
author: wpsze
date: 2025-12-09 06:08:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/h5zLAcM.png
banner_img: https://i.imgur.com/h5zLAcM.png
---

![](https://i.imgur.com/h5zLAcM.png)

# 1. Why the Flat Plate Case is Important in CFD

The flat plate boundary layer problem is a cornerstone in fluid dynamics, and its study using CFD is critical for several reasons:

* **Foundation for Boundary Layer Theory:** The flat plate flow is the simplest geometry that exhibits a **boundary layer**—a thin layer of fluid near the solid surface where viscous effects are significant and the fluid velocity changes from zero (due to the **no-slip condition**) to the free-stream velocity. Understanding this simple case allows you to grasp the core concepts of momentum diffusion and viscous drag.
    
* **Validation and Benchmarking:** Because the flat plate flow (particularly the steady, incompressible, laminar case with zero pressure gradient) has a well-known **analytical solution** (the **Blasius solution**), it serves as a crucial **benchmark** for validating CFD codes, meshing strategies, and turbulence models. If a CFD code cannot accurately predict the flat plate boundary layer, its results for more complex geometries are questionable.
* **Understanding Skin Friction Drag:** The primary force acting on a flat plate parallel to the flow is **skin friction drag**, which arises from the shear stress at the wall. The flat plate case is the perfect model to study the development of this drag and how it's affected by flow conditions (laminar vs. turbulent). This is directly relevant to minimizing drag on aerodynamic surfaces like aircraft wings and turbine blades.
* **Transition to Turbulence:** The flow over a flat plate typically starts as **laminar** and transitions to **turbulent** flow downstream, depending on the **Reynolds number** ($Re_x$). This transition is a key phenomenon in fluid mechanics, and the flat plate provides a basic, controlled environment to study and model this complex change in flow regime.

# 2. Which Aspects to Focus On in CFD Simulation

When performing a CFD simulation of flow over a flat plate, you should focus on the following core aspects to ensure accuracy and gain meaningful insights:

### A. Boundary Layer Resolution (Meshing)
The boundary layer is very thin and characterized by steep velocity gradients near the wall.

* **Fine Mesh Near the Wall:** You must use a very fine, highly refined mesh (often with structured, inflation layers) in the direction **normal** to the plate surface to accurately resolve the velocity profile within the boundary layer.
* **$y^+$ Value:** For turbulent flows, the dimensionless wall distance, **$y^+$**, is the single most critical parameter. It dictates the size of the first cell layer adjacent to the wall.
    * For **Low-Reynolds (Near-Wall) Models** (which resolve the viscous sublayer), the first cell center must be placed in the viscous sublayer, meaning **$y^+ \approx 1$**.
    * For **Wall Function Models** (which use an empirical law-of-the-wall approach), the first cell center must be in the logarithmic layer, meaning **$30 < y^+ < 500$** (or similar range depending on the solver).

### B. Flow Regime and Modeling
You must correctly account for whether the flow is laminar or turbulent.

* **Laminar Flow:** For low Reynolds numbers (typically $Re_L < 500,000$ based on plate length), a laminar model (solving the full Navier-Stokes equations or simplified boundary layer equations) is sufficient. Validation against the Blasius solution is key here.
* **Turbulent Flow:** For higher Reynolds numbers, you need to select and implement an appropriate **Turbulence Model** (e.g., $k-\epsilon$, $k-\omega$ SST, Reynolds Stress Model). The choice of turbulence model directly impacts the predicted skin friction and boundary layer thickness.

### C. Key Result Parameters
Your post-processing should focus on these calculated parameters:

* **Velocity Profiles:** Plotting the velocity magnitude ($u$) versus the distance from the wall ($y$) at various locations ($x$) to observe the growth of the boundary layer and compare against analytical/empirical solutions (like Blasius).
* **Boundary Layer Thickness ($\delta$):** The distance from the wall where $u$ reaches 99% of the free-stream velocity ($U_{\infty}$).
* **Skin Friction Coefficient ($C_f$):** A dimensionless measure of the local shear stress at the wall ($\tau_w$). This is calculated using the velocity gradient at the wall:
    $$C_f = \frac{\tau_w}{\frac{1}{2}\rho U_{\infty}^2} = \frac{\mu (\frac{\partial u}{\partial y})_{y=0}}{\frac{1}{2}\rho U_{\infty}^2}$$
* **Drag Coefficient ($C_D$):** The total drag force ($D$) acting on the plate in a dimensionless form:
    $$C_D = \frac{D}{\frac{1}{2}\rho U_{\infty}^2 L W}$$
    (where $L$ and $W$ are the length and width of the plate).

Focusing on these aspects will give you a solid understanding of how to set up, run, and validate a fundamental CFD case.

# **Blasius boundary layer**

In physics and fluid mechanics, a **Blasius boundary layer** (named after Paul Richard Heinrich Blasius) describes the steady two-dimensional laminar boundary layer that forms on a semi-infinite plate which is held parallel to a constant unidirectional flow. Falkner and Skan later generalized Blasius' solution to wedge flow (Falkner–Skan boundary layer), i.e. flows in which the plate is not parallel to the flow.

- <https://en.wikipedia.org/wiki/Blasius_boundary_layer>

![](https://i.imgur.com/A7IOfk3.png){width=400}

The **Blasius solution** is a classic and foundational piece of fluid dynamics, essential for understanding laminar boundary layers. It's considered the **exact** similarity solution for the steady, two-dimensional, incompressible laminar flow over a semi-infinite flat plate with zero pressure gradient.

<iframe width="560" height="315" src="https://www.youtube.com/embed/ThXIVDTjpb4?si=tGwld1s6QCQxvd56" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 1. Governing Equations & Simplification

The Blasius solution starts with the **Prandtl boundary layer equations** for steady, incompressible, 2D flow. For a flat plate parallel to the flow, the pressure gradient ($\partial p / \partial x$) is zero.

* **Continuity Equation:**
    $$\frac{\partial u}{\partial x} + \frac{\partial v}{\partial y} = 0$$
* **x-Momentum Equation:**
    $$u \frac{\partial u}{\partial x} + v \frac{\partial u}{\partial y} = \nu \frac{\partial^2 u}{\partial y^2}$$
    (where $u$ is the streamwise velocity, $v$ is the wall-normal velocity, $x$ and $y$ are the coordinates, and $\nu$ is the kinematic viscosity).

This is a system of **coupled, non-linear partial differential equations (PDEs)**.

## 2. The Similarity Transformation

The genius of Blasius's approach (a student of Prandtl) was recognizing that the **velocity profiles** at different $x$ locations along the plate are *self-similar*—they all collapse onto a single curve when plotted using the correct dimensionless variables.

### A. Stream Function ($\psi$)
The continuity equation is satisfied identically by introducing the stream function $\psi(x, y)$, where:
$$u = \frac{\partial \psi}{\partial y}, \quad v = - \frac{\partial \psi}{\partial x}$$

### B. Similarity Variable ($\eta$)
Blasius introduced a dimensionless **similarity variable** $\eta$ (eta), which effectively normalizes the $y$ coordinate with the boundary layer thickness $\delta(x)$, which is known to grow as $\sqrt{x}$:

$$\eta = y \sqrt{\frac{U_{\infty}}{\nu x}}$$

### C. Dimensionless Stream Function ($f$)
A dimensionless stream function, $f(\eta)$, is defined to transform the velocity components:

$$\psi(x, y) = \sqrt{\nu x U_{\infty}} f(\eta)$$

* This leads to the streamwise velocity component:
    $$\frac{u}{U_{\infty}} = f'(\eta)$$
    (where the prime denotes differentiation with respect to $\eta$)

### D. The Blasius Equation
Substituting these definitions into the momentum PDE transforms it into a single, non-linear **ordinary differential equation (ODE)**:

$$f''' + \frac{1}{2} f f'' = 0$$

This is the **Blasius equation**. It's a third-order, non-linear ODE, and its solution, $f(\eta)$, provides the universal velocity profile for the laminar flat plate.

## 3. Boundary Conditions (BCs)

The ODE must be solved subject to three boundary conditions:

* **1. No-Slip at the Wall:** The velocity $u$ is zero at $y=0$.
    $$u(x, 0) = 0 \implies f'(0) = 0$$
* **2. Impermeable Wall:** The wall is a streamline, so $v$ is zero at $y=0$, which translates to $\psi$ being constant (set to zero) at the wall.
    $$v(x, 0) = 0 \implies f(0) = 0$$
* **3. Free-Stream Condition:** The velocity $u$ approaches the free-stream velocity $U_{\infty}$ far from the wall.
    $$u(x, y \to \infty) \to U_{\infty} \implies f'(\eta \to \infty) = 1$$

## 4. Solving and Key Results

The Blasius equation has no simple **closed-form analytical solution** and must be solved numerically (typically using the **shooting method**, which is a type of numerical integration).

### A. The Universal Velocity Profile 

The solution for $f'(\eta) = u/U_{\infty}$ is a single, **universal curve**. Any laminar velocity profile on a flat plate, regardless of $x$, $U_{\infty}$, or $\nu$, will map onto this single curve when plotted in terms of the similarity variables $\eta$.

### B. Boundary Layer Thickness ($\delta$)

The boundary layer edge is conventionally defined as the point where $u = 0.99 U_{\infty}$. Numerically solving the Blasius equation shows that this occurs at approximately $\eta = 5.0$. Using the definition of $\eta$, we can find the dimensional boundary layer thickness:

$$\delta \approx 5.0 \sqrt{\frac{\nu x}{U_{\infty}}} \quad \implies \quad \frac{\delta}{x} \approx \frac{5.0}{\sqrt{Re_x}}$$
where $Re_x = \frac{U_{\infty} x}{\nu}$ is the local Reynolds number. This confirms that the boundary layer thickness grows proportional to $x^{1/2}$ and is inversely proportional to $\sqrt{Re_x}$.

### C. Wall Shear Stress ($\tau_w$) and Skin Friction Coefficient ($C_f$)

The wall shear stress is proportional to the slope of the velocity profile at the wall:
$$\tau_w = \mu \left( \frac{\partial u}{\partial y} \right)_{y=0}$$
In terms of the Blasius solution, the key numerical value found is:
$$f''(0) \approx 0.332$$

This allows the local skin friction coefficient ($C_f$) to be determined as:
$$C_f = \frac{\tau_w}{\frac{1}{2}\rho U_{\infty}^2} = \frac{0.664}{\sqrt{Re_x}}$$

### D. Total Drag Coefficient ($C_D$)

Integrating the local skin friction coefficient over the plate length $L$ yields the total drag coefficient:
$$C_D = \frac{1.328}{\sqrt{Re_L}}$$
where $Re_L = \frac{U_{\infty} L}{\nu}$.

The Blasius solution is a powerful example of how a complex system of PDEs can be reduced to a single ODE via a **similarity solution**, providing exact, benchmarkable results for CFD.

# Meshing in OpenFOAM

Meshing the flat plate problem in **OpenFOAM** is a classic case study that centers entirely on accurately resolving the **boundary layer**. You'll typically use the built-in **`blockMesh`** utility to create a structured, high-quality mesh.

The main consideration for a flat plate mesh is the **flow regime** (laminar or turbulent), as this **determines the required cell size near the wall**, quantified by the dimensionless wall distance, $\mathbf{y^+}$.

## 1. Using `blockMesh` for a Flat Plate Domain

For a 2D flat plate (simulated in 3D using a single cell thick domain with `empty` front/back boundaries), the domain is usually a simple rectangular block.

### A. Define the Domain and Blocks

* **Structured Mesh:** `blockMesh` creates structured, hexahedral cells, which are ideal for boundary layer resolution and computational efficiency.
* **Multi-Block Strategy (Recommended):** While a single block can work for very simple cases, using **two or more blocks** allows for better control:
    * One block near the plate to capture the boundary layer growth.
    * A larger block above to handle the free stream flow.
* **Leading Edge:** For high-accuracy simulations, you may need a very fine mesh near the **leading edge** (start) of the plate, as this is where the boundary layer begins and gradients are steepest.

### B. Mesh Expansion/Grading
The key to boundary layer meshing is stretching the cells away from the plate. You use the **`simpleGrading`** keyword in your `blockMeshDict` to define the ratio of the cell size at the far end of the block edge to the cell size at the start of that edge.

* **Near the Plate (y-direction):** Use a high grading ratio (e.g., `simpleGrading (1 20 1)`) in the wall-normal (y) direction to ensure the smallest cells are adjacent to the plate (wall).
* **Along the Plate (x-direction):** The boundary layer grows as $\sqrt{x}$, so the cells should be progressively stretched along the plate (x-direction) to capture the boundary layer thickness increase and ensure the **aspect ratio** ($L_x/L_y$) of the cells doesn't become excessively high far from the wall.

## 2. The Critical Parameter: $\mathbf{y^+}$

The choice of your **first cell height** ($\Delta y_1$) is dictated by the **$y^+$** value required by your turbulence model's **wall treatment**.

$$y^+ = \frac{u_{\tau} \Delta y_1}{\nu}$$

where $u_{\tau}$ is the **friction velocity** and $\nu$ is the **kinematic viscosity**.

### A. Laminar Flow (Validation Case)

* **Meshing Requirement:** For a pure laminar case (e.g., validating against the **Blasius solution** at low Reynolds numbers), you generally need a very **fine mesh** throughout the entire boundary layer, especially near the wall, to resolve the parabolic velocity profile with high accuracy.
* **Focus:** Ensure you have enough cells (e.g., 20-30 or more) *within* the expected boundary layer thickness ($\delta \approx 5.0/\sqrt{Re_x}$) at the plate's trailing edge. **$y^+$ is not strictly relevant**, but $\Delta y_1$ should be very small.

### B. Turbulent Flow (RANS)

For RANS turbulence models (like $k-\epsilon$ or $k-\omega$ SST), your $y^+$ requirement determines the wall-normal mesh strategy:

| Wall Treatment | Target $\mathbf{y^+}$ for First Cell | OpenFOAM Boundary Condition (Example) | Meshing Strategy |
| :--- | :--- | :--- | :--- |
| **Wall Functions (WF)** | $\mathbf{30 < y^+ < 500}$ | `kqRWallFunction`, `epsilonWallFunction`, `nutkWallFunction` | The first cell is placed in the **logarithmic region**, bridging the viscous sublayer. This saves cell count but relies on empirical laws. |
| **Low-Reynolds / Near-Wall Resolving** | $\mathbf{y^+ \approx 1}$ | `kLowReWallFunction`, `nutLowReWallFunction` (or often implicit in the model, like $k-\omega$ SST's blending) | The first cell is placed deep within the **viscous sublayer** ($y^+ < 5$). This is highly accurate but computationally expensive, requiring many more cells near the wall. |

## 3. Practical Steps for Calculating $\Delta y_1$

Since $u_{\tau}$ depends on the solution (the shear stress), you must **estimate** the required $\Delta y_1$ using an empirical formula, typically based on the skin friction coefficient ($C_f$) for a flat plate:

1.  **Estimate Friction Velocity ($u_{\tau}$):** Use the turbulent flat plate skin friction formula (or a similar empirical relation) at the **trailing edge** (where $\delta$ is largest and $u_{\tau}$ is lowest) to estimate the friction velocity:
    $$u_{\tau} = U_{\infty} \sqrt{\frac{C_f}{2}}$$
    (where $C_f \approx 0.059 \cdot Re_L^{-0.2}$ for turbulent flow).
2.  **Calculate First Cell Height ($\Delta y_1$):** Rearrange the $y^+$ definition for the desired target $y^+$:
    $$\Delta y_1 = \frac{y^+ \nu}{u_{\tau}}$$
3.  **Use `blockMesh` Grading:** Input this calculated $\Delta y_1$ into your `blockMeshDict` by setting the total number of cells ($N_y$) and the expansion ratio for the block adjacent to the wall. OpenFOAM also offers the **`nutUSpaldingWallFunction`** which is a blended function that works across most $y^+$ values (though $\mathbf{y^+} \approx 1$ or $\mathbf{y^+} > 30$ are still best practice targets).

*It's crucial to run the simulation, check the resulting **`yPlus`** field using the `yPlus` function object in OpenFOAM, and refine the mesh (perform a **Grid Convergence Study**) until your $y^+$ values meet your chosen wall treatment criteria.* 

# Turbulence model activation issue

- [CFD | Turbulence model activation](https://waipangsze.github.io/2025/12/19/CFD-Turbulence-model-activation/)

# OpenFOAM壁面y+验证：低雷诺数模型

- [CFD中文网 - OpenFOAM壁面y+验证：低雷诺数模型](https://www.cfd-china.com/topic/6507/openfoam%E5%A3%81%E9%9D%A2y-%E9%AA%8C%E8%AF%81-%E4%BD%8E%E9%9B%B7%E8%AF%BA%E6%95%B0%E6%A8%A1%E5%9E%8B?lang=en-US)
- [CFD中文网  - LES直流槽道边界层模拟，如何得到正则化速度u+以及正则化坐标y+？](https://www.cfd-china.com/topic/6248/les%E7%9B%B4%E6%B5%81%E6%A7%BD%E9%81%93%E8%BE%B9%E7%95%8C%E5%B1%82%E6%A8%A1%E6%8B%9F-%E5%A6%82%E4%BD%95%E5%BE%97%E5%88%B0%E6%AD%A3%E5%88%99%E5%8C%96%E9%80%9F%E5%BA%A6u-%E4%BB%A5%E5%8F%8A%E6%AD%A3%E5%88%99%E5%8C%96%E5%9D%90%E6%A0%87y?_=1690794589667)

# References

1. [Fluid_Mechanics_Lesson_13D.pdf **](https://www.me.psu.edu/cimbala/me320/Lesson_Notes/Fluid_Mechanics_Lesson_13D.pdf)
2. [NASA Turbulent Flat Plate Validation Benchmark](https://www.cfdsupport.com/flat-plate-benchmark/)
3. [Johns Hopkins Turbulence Databases](https://turbulence.pha.jhu.edu/Channel_Flow.aspx)
   1. Channel flow
