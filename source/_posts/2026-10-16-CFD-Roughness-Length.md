---
layout: post
title: NWP | CFD | Roughness Length K_s/z0
categories: [NWP]
tags: [NWP, MPAS, WRF, OpenFOAM, CFD, HPC, Roughness Length, K_s, z0]
author: wpsze
date: 2026-10-16 06:08:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/tCo9ler.png
banner_img: https://i.imgur.com/tCo9ler.png
---

From Gemini

---

While both parameters quantify surface roughness in fluid dynamics and boundary layer meteorology, they are defined in fundamentally different contexts and represent different equivalent scales.

| Metric | Equivalent Sand-Grain Roughness Length ($k_s$) | Aerodynamic Roughness Length ($z_0$) |
| --- | --- | --- |
| **Primary Domain** | Pipe/conduit flow, hydraulics, engineered surfaces (Nikuradse / Moody chart) | Atmospheric boundary layer, environmental fluid mechanics, meteorology |
| **Physical Concept** | Diameter of uniform, tightly packed silica sand grains that yields the same friction loss | Height above the ground at which logarithmic velocity profile extrapolates to zero |
| **Typical Scale** | Physical grain height (millimeters to centimeters) | Micro-scale distance parameter (often $z_0 \approx k_s / 30$) |
| **Main Equation** | Used in friction factor $f(Re, k_s/D)$ (e.g., Colebrook-White) | Used in the log-law velocity profile: $u(z) = \frac{u_*}{\kappa} \ln\left(\frac{z}{z_0}\right)$ |

{% note primary %}
Aerodynamic Roughness Length ($z_0$): Height above the ground at which logarithmic velocity profile extrapolates to zero
{% endnote %}

---

## Key Conceptual Differences

### 1. Definition and Physical Origin

* **Equivalent Sand-Grain Roughness ($k_s$):** Introduced by Johann Nikuradse through experiments where uniform grains of sand were glued inside pipes. It maps any arbitrary surface roughness (corrugated walls, commercial steel, concrete) to an equivalent, uniform sand-grain size that produces the exact same hydraulic head loss or friction factor $f$ at high Reynolds numbers.
* **Aerodynamic Roughness Length ($z_0$):** Originates from Prandtl's boundary layer theory and logarithmic velocity distribution over rough boundaries. It is the virtual zero-velocity height ($u(z_0) = 0$) obtained by extrapolating the mean logarithmic wind profile down toward the wall or ground.

### 2. Relationship Between $k_s$ and $z_0$

In fully rough turbulent wall-bounded flows, the logarithmic law of the wall can be written in two equivalent standard forms:

**Pipe / Engineering Form ($k_s$):**


$$u(z)^+ = \frac{1}{\kappa} \ln\left(\frac{z}{k_s}\right) + B$$


*(where $\kappa \approx 0.41$ is the von Kármán constant, and $B \approx 8.5$ for fully rough surfaces)*

**Atmospheric / Boundary Layer Form ($z_0$):**


$$u(z)^+ = \frac{1}{\kappa} \ln\left(\frac{z}{z_0}\right)$$

Equating the two formulations yields the exact mathematical conversion:


$$B = -\frac{1}{\kappa} \ln(z_0 / k_s) \implies \frac{z_0}{k_s} = e^{-\kappa B}$$

Substituting standard boundary layer values ($\kappa = 0.40–0.41$ and $B = 8.5$):


$$\frac{z_0}{k_s} \approx \frac{1}{30} \quad \left(\text{or } z_0 \approx 0.033 \, k_s\right)$$

This means the aerodynamic roughness length $z_0$ is roughly **$1/30^{\text{th}}$** of the physical equivalent sand-grain roughness height $k_s$.

---

## Conversion Summary for Practical Engineering

* **To find $z_0$ from sand roughness $k_s$:**

$$z_0 \approx \frac{k_s}{30}$$


* **To find $k_s$ from aerodynamic roughness $z_0$:**

$$k_s \approx 30 \cdot z_0$$

![<https://www2.mmm.ucar.edu/wrf/users/tutorial/presentation_pdfs/202101/dudhia_physics_surface.pdf>](https://i.imgur.com/7Me4fSB.png)
![Abolafia-Rosenzweig, R., He, C., Burns, S. P., & Chen, F. (2021). Implementation and evaluation of a unified turbulence parameterization throughout the canopy and roughness sublayer in Noah-MP snow simulations. Journal of Advances in Modeling Earth Systems, 13, e2021MS002665. https://doi.org/10.1029/2021MS002665 ](https://i.imgur.com/tCo9ler.png)