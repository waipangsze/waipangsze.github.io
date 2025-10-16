---
layout: post
title: NWP | Boussinesq and Anelastic Approximations in Atmospheric Science
categories: [MPAS]
tags: [MPAS, NWP, WRF, Boussinesq, Anelastic, density]
author: wpsze
date: 2025-10-15 23:18:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/LzfLUBJ.png
banner_img: https://i.imgur.com/LzfLUBJ.png
---

# Simplifying the Skies: An Introduction to Boussinesq and Anelastic Approximations in Atmospheric Science

In the quest to understand and predict the complex motions of the atmosphere, scientists rely on mathematical models. However, the full equations governing fluid dynamics are notoriously difficult to solve. To make these calculations more manageable, physicists and meteorologists employ a range of simplifying assumptions known as approximations. Among the most fundamental of these are the **Boussinesq and anelastic approximations**, which are crucial for modeling a variety of atmospheric phenomena, from small-scale convection to large-scale circulation.

{% note primary %}
Both approximations aim to simplify the **treatment of density variations in the governing equations of motion**
{% endnote %}

At their core, both approximations aim to simplify the **treatment of density variations in the governing equations of motion**. In the atmosphere, density changes are driven by variations in temperature, pressure, and moisture content. These density differences, in turn, create buoyancy forces that drive vertical air motion. However, accounting for the full effect of these density changes on all aspects of the airflow can be computationally expensive. The Boussinesq and anelastic approximations offer a way to capture the essential impact of buoyancy while simplifying other terms in the equations.


![](https://i.imgur.com/LzfLUBJ.png){width=400}

---

## The Boussinesq Approximation: A Focus on Buoyancy

The **Boussinesq approximation**, named after the French physicist Joseph Boussinesq, is the more restrictive of the two. It is founded on the principle that density variations are only significant in the term that accounts for the force of gravity—the buoyancy term. In all other terms of the momentum and continuity equations, density is treated as a constant.

### Key Assumptions:

* **Small Density Perturbations:** The density variations ($\rho'$) from a constant reference density ($\rho_0$) are assumed to be much smaller than the reference density itself ($\rho' \ll \rho_0$).
* **Incompressibility:** The flow is assumed to be incompressible, meaning that the divergence of the velocity field is zero ($\nabla \cdot \mathbf{u} = 0$). This **effectively filters out sound waves**, which are associated with the compressibility of air.
* **Shallow Vertical Motions:** This approximation is most valid for atmospheric phenomena where the vertical scale of motion is much smaller than the scale height of the atmosphere (approximately 8-10 km). The scale height is the vertical distance over which the atmospheric density changes by a factor of *e* (approximately 2.718).

### Applications and Limitations:

The Boussinesq approximation is well-suited for modeling **shallow convection**, such as the formation of fair-weather cumulus clouds, boundary layer thermals, and some types of thunderstorms where the vertical extent is limited.

However, its primary limitation is its inapplicability to **deep atmospheric phenomena**. For motions that span a significant portion of the troposphere, such as deep convective storms or large-scale atmospheric waves, **the assumption of a constant reference density breaks down**. As an air parcel rises through a deep layer of the atmosphere, the ambient pressure and density decrease significantly, a factor the Boussinesq approximation cannot adequately capture.

---

## The Anelastic Approximation: Accounting for a Stratified Atmosphere

The **anelastic approximation** offers a more refined approach by acknowledging that atmospheric density varies significantly with height. Instead of assuming a constant reference density, it considers a background density that decreases exponentially with altitude, which is a more realistic representation of the atmosphere's basic state.

### Key Assumptions:

* **Small Perturbations from a Stratified Reference State:** Density and pressure are considered as small perturbations from a hydrostatically balanced reference state that varies with height ($\rho_0(z)$ and $p_0(z)$).
* **Filtering of Sound Waves:** Like the Boussinesq approximation, the anelastic approximation filters out sound waves. It achieves this by modifying the continuity equation to $\nabla \cdot (\rho_0 \mathbf{u}) = 0$. This allows for the expansion and compression of air parcels as they move vertically through the stratified atmosphere, but it does so in a way that does not support the propagation of acoustic waves.
* **Low Mach Number:** The fluid velocities are assumed to be much smaller than the speed of sound.

### Applications and Limitations:

The anelastic approximation has a broader range of applications than the Boussinesq approximation. It is the foundation for many numerical models used to simulate **deep convection**, including severe thunderstorms and mesoscale convective systems. It is also employed in modeling larger-scale phenomena like mountain waves and atmospheric tides.

While more versatile, the anelastic approximation is more complex to implement than the Boussinesq approximation. **Its primary limitation is that it still filters out sound waves**, which, while often negligible, can be important in specific atmospheric contexts, such as the initial stages of explosive volcanic eruptions or certain types of atmospheric gravity waves.

---

## A Tale of Two Approximations: Boussinesq vs. Anelastic

| Feature | Boussinesq Approximation | Anelastic Approximation |
| :--- | :--- | :--- |
| **Reference Density** | Assumed to be a constant ($\rho_0$). | Varies with height ($\rho_0(z)$). |
| **Continuity Equation** | $\nabla \cdot \mathbf{u} = 0$ (Incompressible) | $\nabla \cdot (\rho_0 \mathbf{u}) = 0$ |
| **Conservation** | Conserves **volume** (incompressible). | Conserves **mass**. |
| **Applicable Depth** | **Shallow** flows (small vertical scale). | **Deep** flows (large vertical scale), correctly captures density stratification. |
| **Typical Applications** | Shallow cumulus clouds, boundary layer thermals. | Deep convective storms, mountain waves. |
| **Complexity** | Simpler to implement. | More complex to implement. |
| **Relationship** | The Boussinesq equations are a **simplified subset** of the anelastic equations, applicable under the additional constraint of a shallow layer. | |

In essence, the Boussinesq approximation can be seen as a special case of the anelastic approximation where the atmospheric density is assumed to be uniform. The choice between the two depends on the specific atmospheric phenomenon being studied. For shallow systems where density variations with height are negligible, the simplicity of the Boussinesq approximation is advantageous. However, for deep atmospheric motions where the background density stratification plays a crucial role, the more physically realistic anelastic approximation is the necessary choice. These powerful tools, by simplifying the complexities of atmospheric motion, have become indispensable in our ongoing efforts to unravel the workings of the sky.

#  **Boussinesq Approximation**

The **Boussinesq Approximation** simplifies the full governing equations of a compressible fluid by assuming density variations are small, except where they are coupled with gravity (the buoyancy term). This set of equations is suitable for **shallow, buoyant flows** where the vertical scale of the motion is much less than the atmospheric scale height.

The equations are expressed in terms of perturbations around a constant reference state.

## 1. Variables

* $\mathbf{u}$: velocity vector ($u, v, w$)
* $\rho_0$: **constant** reference density
* $\theta_0$: **constant** reference potential temperature
* $\Phi$: "reduced" or "modified" pressure (kinematic pressure, $\Phi = p'/\rho_0$)
* $b$: buoyancy ($b = g \theta' / \theta_0$)
* $\theta'$: perturbation potential temperature ($\theta' = \theta - \theta_0$)
* $D/Dt$: the material derivative, $D/Dt = \partial/\partial t + \mathbf{u} \cdot \nabla$
* $\nu$: kinematic viscosity
* $\kappa$: thermal diffusivity
* $Q_{\text{heat}}$: diabatic heating rate per unit mass

***

## 2. Boussinesq Equations

### 1. Mass Continuity Equation (Incompressibility)
The flow is treated as **incompressible** (volume-conserving), which filters out sound waves.

$$\nabla \cdot \mathbf{u} = \frac{\partial u}{\partial x} + \frac{\partial v}{\partial y} + \frac{\partial w}{\partial z} = 0$$

### 2. Momentum Equation (Navier-Stokes)

The horizontal (X and Y) and vertical (Z) momentum equations are expressed in terms of the constant reference density $\rho_0$.

**Horizontal Momentum (Vector Form, simplified):**
$$\frac{D \mathbf{u}_h}{D t} = - \nabla_h \Phi + \nu \nabla^2 \mathbf{u}_h$$
where $\mathbf{u}_h = (u, v)$ and $\nabla_h = (\partial/\partial x, \partial/\partial y)$.

**Vertical Momentum:**
$$\frac{D w}{D t} = - \frac{\partial \Phi}{\partial z} + b + \nu \nabla^2 w$$

* The term $\frac{\partial \Phi}{\partial z}$ is the **vertical pressure gradient force**.
* The term **$b = g \frac{\theta'}{\theta_0}$ is the buoyancy force**, which is the only term where density/temperature variations are explicitly retained.

### 3. Thermodynamic Energy Equation (Potential Temperature)

This equation describes the evolution of the **buoyancy field** (or potential temperature perturbation, $\theta'$), including non-adiabatic effects like heating and diffusion.

$$\frac{D \theta'}{D t} = w \frac{\partial \theta_0}{\partial z} + \frac{Q_{\text{heat}}}{\Pi} + \kappa \nabla^2 \theta'$$
* The term $w \frac{\partial \theta_0}{\partial z}$ (or $-w \frac{\partial \bar{\theta}}{\partial z}$ in some forms) represents the **advection of the background potential temperature gradient** (stratification) by vertical motion. For a Boussinesq atmosphere, $\frac{\partial \theta_0}{\partial z}$ is often assumed to be a small, non-zero constant.
* $\Pi$ is the Exner function. In the buoyancy form ($D b / D t$), the equation often simplifies to:
$$\frac{D b}{D t} = - w N^2 + \frac{g}{\theta_0} \left( \frac{Q_{\text{heat}}}{\Pi} + \kappa \nabla^2 \theta' \right)$$
where $N^2 = \frac{g}{\theta_0} \frac{\partial \theta_0}{\partial z}$ is the square of the **Brunt–Väisälä frequency**.

# **Anelastic Approximation**

The **Anelastic Approximation** is a simplification of the full set of fluid dynamic equations for a compressible, stratified fluid (like the atmosphere or stellar interiors) that filters out fast-moving sound waves by neglecting the local time derivative of density in the continuity equation. This makes the continuity equation diagnostic (elliptic) rather than prognostic (hyperbolic).

The equations are typically written in terms of total variables (like density $\rho_{tot}$ and potential temperature $\theta_{tot}$) split into a hydrostatic, time-independent **reference state** (denoted by a subscript 0) and small **perturbations** (denoted by a prime), for example, $\rho_{tot} = \rho_0(z) + \rho'(x, y, z, t)$ and $\theta_{tot} = \theta_0(z) + \theta'(x, y, z, t)$.

The most common form of the anelastic equations for a dry atmosphere, neglecting rotation, viscosity, and diabatic heating for simplicity, is given below.

---

## Anelastic Governing Equations

### 1. Mass Continuity Equation (Anelastic Constraint)

This is the defining equation of the anelastic approximation. It states that the mass flux of the reference state is non-divergent:

$$\nabla \cdot (\rho_0 \mathbf{u}) = 0$$

where:
* $\rho_0(z)$ is the **reference state density**, which varies only with height $z$.
* $\mathbf{u} = (u, v, w)$ is the **velocity vector** (perturbation velocity).

This constraint filters out sound waves and implies that the local time derivative of the total density, $\partial \rho_{tot} / \partial t$, is negligible compared to the advection terms. In component form, this is:

$$\frac{\partial (\rho_0 u)}{\partial x} + \frac{\partial (\rho_0 v)}{\partial y} + \frac{\partial (\rho_0 w)}{\partial z} = 0$$

### 2. Momentum Equations (Navier-Stokes)

The momentum equations describe the acceleration of the fluid. The horizontal components are often simplified:

#### Horizontal Momentum (in $x$ and $y$ directions):
$$\rho_0 \frac{D \mathbf{u}_h}{D t} = - \nabla_h p'$$

where:
* $\frac{D}{D t} = \frac{\partial}{\partial t} + \mathbf{u} \cdot \nabla$ is the **Lagrangian material derivative**.
* $\mathbf{u}_h = (u, v)$ is the **horizontal velocity vector**.
* $\nabla_h$ is the horizontal gradient operator.
* $p'$ is the **perturbation pressure**.

#### Vertical Momentum (in $z$ direction):
$$\rho_0 \frac{D w}{D t} = - \frac{\partial p'}{\partial z} + \rho_0 g B$$

where:
* $w$ is the **vertical velocity**.
* $g$ is the **acceleration due to gravity**.
* $B$ is the **buoyancy term** (buoyancy force per unit mass), often expressed in terms of the potential temperature perturbation $\theta'$:
$$B = g \frac{\theta'}{\theta_0}$$

This equation incorporates the **hydrostatic balance** of the reference state and accounts for the non-hydrostatic pressure forces and the buoyancy force acting on the fluid perturbation.

### 3. Thermodynamic Energy Equation

The anelastic approximation is usually closed using an equation for the conservation of **potential temperature** ($\theta$):

$$\rho_0 \frac{D \theta'}{D t} = -\rho_0 w \frac{d \theta_0}{d z} + \frac{Q}{c_p \Pi}$$

where:
* $\theta'$ is the **perturbation potential temperature** ($\theta_{tot} = \theta_0 + \theta'$).
* $\theta_0(z)$ is the **reference state potential temperature**.
* $\frac{d \theta_0}{d z}$ is the **vertical gradient of the reference potential temperature** (related to the static stability).
* $Q$ is the **diabatic heating rate** per unit volume (e.g., radiative, latent heat).
* $c_p$ is the **specific heat at constant pressure**.
* $\Pi = c_p (p_{tot}/p_{ref})^\kappa$ is the **Exner function** ($\kappa = R/c_p$).

This equation is a prognostic equation for $\theta'$. The term $-\rho_0 w \frac{d \theta_0}{d z}$ represents the conversion of kinetic energy to potential energy due to vertical motion in a stratified environment.

# AI source

From Google Gemini 2.5

# References

1. [Xu, L., S. Raman, and R. V. Madala. "A review of non-hydrostatic numerical models for the atmosphere." 1st World Congress of Nonlinear Analysis Fire and Forest Meteorology. Nonlinear World, Walter de Gruyter, New York, Tampa, FL. 1992.](https://sraman.wordpress.ncsu.edu/files/2020/09/J98.pdf)
2. [Introduction to Boussinesq and anelastic approximations for the atmosphere | MIT](https://pog.mit.edu/src/810/anelastic_boussinesq_intro.pdf)
3. [Durran, D. R., 1989: Improving the Anelastic Approximation. J. Atmos. Sci., 46, 1453–1461, https://doi.org/10.1175/1520-0469(1989)046<1453:ITAA>2.0.CO;2.](https://journals.ametsoc.org/view/journals/atsc/46/11/1520-0469_1989_046_1453_itaa_2_0_co_2.xml)