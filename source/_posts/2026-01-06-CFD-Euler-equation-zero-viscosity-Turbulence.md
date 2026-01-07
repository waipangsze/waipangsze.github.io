---
layout: post
title: CFD | Euler equations | zero viscosity | Turbulence
categories: [CFD]
tags: [OpenFOAM, Euler equations, viscosity, Turbulence]
author: wpsze
date: 2026-01-06 06:08:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/YcwF7P3.gif
banner_img: https://i.imgur.com/YcwF7P3.gif
---

# Non-Linear Term

In Computational Fluid Dynamics (CFD), the transition from the base **Navier-Stokes equations** to turbulence modeling involves understanding how nonlinearity behaves and how averaging creates new mathematical "holes" that we must fill.

## 1. The Source of the Non-Linear Term

The non-linearity in the Navier-Stokes equations comes from the **convective acceleration term** (also called the **advection term**).

In the momentum equation, this is typically written as:

$$(\mathbf{u} \cdot \nabla) \mathbf{u}$$

**Why is it non-linear?** It is a product of the velocity field and its own gradient. Because the variable we are solving for ($u$) is multiplied by itself, the equation is no longer linear. Physically, this term represents **inertia**—how the fluid’s own momentum transports itself through space. This self-interaction is what allows small disturbances to grow and eventually become chaotic (turbulence).

## 2. The Source of Reynolds Stress

When we perform **Reynolds Averaging**, we decompose the instantaneous velocity $u$ into a mean component $\bar{u}$ and a fluctuating component $u'$ :


When you plug this decomposition into the non-linear convective term  $(\mathbf{u} \cdot \nabla) \mathbf{u}$ and take the average of the whole equation, the "cross terms" between the mean and fluctuations ($\bar{u}u'$) average to zero. However, the **average of the product of two fluctuations** does not necessarily disappear.

This leaves us with a new term on the right-hand side:

$$u = \bar{u} + u'$$

This is the **Reynolds Stress**. Its source is the **non-linear convective term**. Because the original equation was non-linear, the averaging process "leaks" information from the fluctuations into the mean flow equation.

## 3. If Viscosity is Zero: Reynolds Stress and Turbulence

This touches on the difference between the **Navier-Stokes equations** (viscous) and the **Euler equations** (inviscid/zero viscosity).

| Question | Answer | Explanation |
| --- | --- | --- |
| **Is Reynolds Stress zero?** | **No** | Reynolds stress depends on velocity fluctuations ($u'$), not on viscosity ($\mu$). In a theoretical inviscid fluid, particles can still fluctuate and transport momentum, so Reynolds stress can exist. |
| **Does Turbulence exist?** | **Theoretically, Yes** | Turbulence is driven by inertia (non-linearity), not viscosity. In fact, as viscosity  $\to 0$, the Reynolds number $\to \infty$, making the flow "more" turbulent. |

## The "Catch" with Zero Viscosity

While turbulence can exist without viscosity, it behaves differently:

* **No Energy Dissipation:** In real fluids, viscosity is the "drain" that turns kinetic energy into heat at very small scales (the Kolmogorov scale). Without viscosity, there is no way to stop the energy cascade; energy just keeps moving to smaller and smaller scales forever.
	- **In a viscous fluid**: Energy is injected at large scales (the "Big Whorls"), cascades down to smaller and smaller scales (the "Inertial Subrange"), and is finally converted into heat by viscosity at the **Kolmogorov scale**.
	- **In a zero-viscosity fluid**: The "drain" at the bottom of the cascade is plugged. Because there is no viscosity to turn kinetic energy into heat, the energy keeps cascading to smaller and smaller scales forever.
	- Mathematically, this can lead to **"singularities"** where *velocity gradients become infinite*. In computer simulations of inviscid flow (Euler equations), this often appears as **numerical instability** — the energy builds up at the smallest scales your grid can resolve and "blows up" because it has no physical way to leave the system.
* **The Euler Paradox:** While the Euler equations (zero viscosity) can show chaotic behavior, `they cannot "create" vorticity from smooth boundaries like real fluids do (because there is no "no-slip" condition)`.

**Summary:** Viscosity is not the *source* of turbulence (inertia is), but it is the *stabilizer* that eventually kills it. In a world with zero viscosity, Reynolds stress would still exist as long as there are velocity fluctuations.

# D'Alembert's paradox

- **Potential flow**: In fluid dynamics, potential flow or irrotational flow refers to a description of a fluid flow with no vorticity $\boldsymbol\omega \equiv \nabla\times\mathbf v=0$  in it. Such a description typically arises in the limit of vanishing viscosity, i.e., for an inviscid fluid and with no vorticity present in the flow. 
	- Like any vector field having zero curl, the velocity field can be expressed as the gradient of certain scalar, say $\varphi(\mathbf x,t)$ which is called the **velocity potential**, since the curl of the gradient is always zero.

In fluid dynamics, d'Alembert's paradox (or the hydrodynamic paradox) is a paradox discovered in 1752 by French mathematician Jean le Rond d'Alembert. D'Alembert proved that – 

{% note primary %}
For **incompressible and inviscid** **potential flow** – **the drag force is zero** on a body moving with constant velocity relative to (and simultaneously through) the fluid. 
{% endnote %}

Zero drag is in direct contradiction to the observation of substantial drag on bodies moving relative to and at the same time through a fluid, such as air and water; especially at high velocities corresponding with high Reynolds numbers. It is a particular example of the reversibility paradox.

## Why does this happen?

1. **Symmetry:** In a perfectly inviscid flow, the fluid wraps around the back of an object as smoothly as it approached the front. The high pressure at the front "stagnation point" is perfectly balanced by high pressure at the back.
2. **No Separation:** Without viscosity, there is no "no-slip" condition at the wall. The fluid just slides past. Without a no-slip condition, you don't get the "slow-moving" fluid layer that eventually peels away to create a wake.

## How Turbulence Solves it?

We now know that even if viscosity is zero, the "smooth" (potential flow) solution is **unstable**. In reality, tiny perturbations cause the flow to break down into a "Turbulent Euler" state.

* This turbulence breaks the front-to-back symmetry.
* Even without viscosity, this chaotic motion creates a low-pressure region behind the object (a wake).
* This results in **Pressure Drag**, resolving the paradox even if **Skin Friction** remains zero.

### Summary Table

| Feature | Viscous Turbulence (Navier-Stokes) | Inviscid Turbulence (Euler) |
| --- | --- | --- |
| **Energy** | Dissipated into heat at small scales. | Conserved (or piles up at small scales). |
| **Boundary Layer** | Exists; creates drag via friction and separation. | No boundary layer; "Slip" condition at walls. |
| **Drag** | Skin friction + Pressure drag. | Pressure drag only (due to wake instability). |
| **End State** | Reaches a steady turbulent equilibrium. | Can become mathematically "singular" or chaotic. |

