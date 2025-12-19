---
layout: post
title: CFD | Turbulence model activation (use or not use)
categories: [CFD]
tags: [OpenFOAM, HPC, turbulence model, SST k-omega]
author: wpsze
date: 2025-12-19 06:07:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/h5zLAcM.png
banner_img: https://i.imgur.com/h5zLAcM.png
---

# Question: When should we exclude the turbulence model?

In Computational Fluid Dynamics (CFD), the decision to **omit a turbulence model** is usually driven by the **physics of the flow** or the **resolution of the simulation**. Using a turbulence model when it isn't needed can introduce "artificial" viscosity that dampens real physical features or leads to incorrect results.

# You should not use a turbulence model in the following scenarios

## 1. The Flow is Truly Laminar

If the **Reynolds Number** of your flow is below the **critical threshold for transition**, the flow is laminar. In this regime, viscous forces are strong enough to dampen any chaotic fluctuations.

* **Pipe Flow:** 
* **External Flow (Flat Plate):** 
* **Typical Applications:** Microfluidics, lubrication systems, high-viscosity fluids (like honey or heavy oils), and very slow-moving gases.

You should not use a turbulence model in CFD when the flow is genuinely laminar **(low Reynolds number, typically Re < 2300 in pipes, or low speeds in general)**, when you need extremely high resolution of large turbulent structures (use LES/DES instead of RANS), or when computational cost is a primary constraint and simpler laminar models suffice, but be very careful, **as most real-world flows are turbulent** and turbulence models are often necessary for accuracy, **especially near walls (using SST with fine mesh is common)**. 

## 2. You are Performing DNS (Direct Numerical Simulation)

If you are solving the full **Navier-Stokes equations** and your mesh is fine enough to resolve the smallest possible scales of motion (the **Kolmogorov scales**), you do not use a turbulence model.

* **Why:** A turbulence model (like ) is a "shortcut" to approximate the effects of small eddies. In DNS, you are calculating those eddies directly. Adding a model on top of DNS would be "double-counting" the physics and would ruin the accuracy.

## 3. Unsteady Laminar Phenomena (e.g., Vortex Shedding)

![Animation representing the two-dimensional flow patterns behind a rounded obstacle, known as a Von Kármán vortex street.](https://i.imgur.com/7KaSW3W.png)

Sometimes a flow is **unsteady** but still technically laminar. A classic example is **Von Kármán vortex shedding** at low Reynolds numbers (e.g.,  for a cylinder).

* **Why:** If you use a steady-state RANS (Reynolds-Averaged Navier-Stokes) turbulence model here, the model might "smooth out" the vortices into a single steady wake, failing to capture the periodic oscillation that actually exists.

## 4. Creeping Flow (Stokes Flow)

![Flow visualization of a form of Stokes flow over a circular cylinder sandwiched between glass plates. The dye marks streamlines in water flowing at 1 mm/second. (Photograph by D. H. Peregrine.)](https://i.imgur.com/WtPH3qd.png)

In cases where the Reynolds number is much less than 1, inertial forces are negligible compared to viscous forces.

Stokes flow (named after George Gabriel Stokes), also named creeping flow or creeping motion, is a type of fluid flow where advective inertial forces are small compared with viscous forces. The Reynolds number is low, i.e. $$ Re << 1$$. This is a typical situation in flows where the fluid velocities are very slow, the viscosities are very large, or the length-scales of the flow are very small. Creeping flow was first studied to understand lubrication. In nature, this type of flow occurs in the swimming of microorganisms and sperm. In technology, it occurs in paint, MEMS devices, and in the flow of viscous polymers generally.

* **Why:** Turbulence models are designed to handle the complex interaction between inertia and viscosity. In creeping flow, there is no turbulence to model; the flow is entirely dominated by diffusion.

## 5. Transition Zones (Use Caution)

If your flow starts as laminar and only becomes turbulent halfway through the domain, a standard "fully turbulent" model will be wrong at the start.

* **Instead:** You should either use a **Laminar** setting for the whole domain (if the turbulent part is tiny) or use a specialized **Transition Model** (like ). Avoid standard  or  if the laminar portion is the primary area of interest.

## Summary Table: When to Skip the Model

| Scenario | Flow Regime | Recommended Approach |
| --- | --- | --- |
| **Microfluidics** | Low  | Laminar Solver |
| **High-Fidelity Research** | Any  | DNS (No Model) |
| **Steady slow pipe flow** |  | Laminar Solver |
| **Thin film lubrication** | Very Low  | Creeping Flow / Laminar |

> **Note:** If you are unsure, always calculate your Reynolds number first. If  is in the "Transitional" range, assuming turbulence is often the "safer" engineering choice for drag/heat transfer, but it may overpredict losses.

# **SST k-omega ($k-\omega$)** model and **no turbulence model** (Laminar)

Choosing between the **SST k-omega ($k-\omega$)** model and **no turbulence model** (Laminar) depends entirely on whether your flow physics are dominated by chaotic fluctuations or smooth, orderly layers.

The "better" option is the one that matches the **Reynolds Number (Re)** and the physical behavior of your specific case.

---

## Quick Decision Guide

| Use **Laminar** (No Model) if... | Use **SST k-omega ($k-\omega$)** if... |
| --- | --- |
| **Low :**  (Internal) or  (External). | **High :** Flow is fast, fluid is thin, or geometry is large. |
| **Simple Physics:** Microfluidics, honey/oils, or very slow air. | **Separation:** You expect flow to "detach" from a surface (e.g., an airfoil at an angle). |
| **Direct Numerical Simulation (DNS):** You have a massive supercomputer and an extremely fine mesh. | **Engineering Accuracy:** You need a reliable prediction of lift, drag, or pressure drop in industrial flows. |
| **Stability:** You want to avoid "artificial" damping of small, real laminar features (like a vortex street). | **Transition:** You aren't sure if the flow is turbulent, but want a model that can handle the "boundary" better than . |

---

## Why SST k-omega is the "Safe" Choice

If you are unsure, **SST k-omega** ($k-\omega$) is generally considered the "workhorse" of modern CFD.

* **The Blending Advantage:** It uses the accurate  formulation near walls (good for boundary layers) and switches to  in the free stream (good for stability).
* **Self-Correction:** In many CFD codes, if the flow is truly laminar, the SST  model will naturally predict near-zero turbulent kinetic energy ($k \approx 0$). This effectively "turns off" the turbulence model, making it behave like a laminar solver.

## When "No Model" is Actually Better

Using SST  on a **truly laminar** flow can sometimes cause issues:

1. **False Viscosity:** The model might calculate a tiny amount of "eddy viscosity" that doesn't exist. This can lead to a **30% error in drag coefficients** for very low-speed flows (like flow over a sphere at $Re=10$).
2. **Damping:** It can accidentally "smooth out" real, unsteady laminar structures, such as the oscillating wake behind a cylinder at .
3. **Computational Cost:** Solving two extra equations ($k$ and $\omega$ ) takes more memory and time. If the flow is laminar, you are paying for math you don't need.

# References

1. [Turbulence model activation](https://www.cfd-online.com/Forums/main/239115-turbulence-model-activation.html#:~:text=Most%20flows%20that%20are%20encountered%20in%20reality,use%20a%20turbulent%20model%20in%20most%20situations.)
	1. Most flows that are encountered in reality are turbulent. Laminar flows are the exception not the rule. So be prepared to use a turbulent model in most situations.
