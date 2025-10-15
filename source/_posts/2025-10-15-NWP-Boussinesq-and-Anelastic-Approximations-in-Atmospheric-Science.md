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

## Simplifying the Skies: An Introduction to Boussinesq and Anelastic Approximations in Atmospheric Science

In the quest to understand and predict the complex motions of the atmosphere, scientists rely on mathematical models. However, the full equations governing fluid dynamics are notoriously difficult to solve. To make these calculations more manageable, physicists and meteorologists employ a range of simplifying assumptions known as approximations. Among the most fundamental of these are the **Boussinesq and anelastic approximations**, which are crucial for modeling a variety of atmospheric phenomena, from small-scale convection to large-scale circulation.

{% note primary %}
Both approximations aim to simplify the **treatment of density variations in the governing equations of motion**
{% endnote %}

At their core, both approximations aim to simplify the **treatment of density variations in the governing equations of motion**. In the atmosphere, density changes are driven by variations in temperature, pressure, and moisture content. These density differences, in turn, create buoyancy forces that drive vertical air motion. However, accounting for the full effect of these density changes on all aspects of the airflow can be computationally expensive. The Boussinesq and anelastic approximations offer a way to capture the essential impact of buoyancy while simplifying other terms in the equations.


![](https://i.imgur.com/LzfLUBJ.png){width=400}

---

### The Boussinesq Approximation: A Focus on Buoyancy

The **Boussinesq approximation**, named after the French physicist Joseph Boussinesq, is the more restrictive of the two. It is founded on the principle that density variations are only significant in the term that accounts for the force of gravity—the buoyancy term. In all other terms of the momentum and continuity equations, density is treated as a constant.

#### Key Assumptions:

* **Small Density Perturbations:** The density variations ($\rho'$) from a constant reference density ($\rho_0$) are assumed to be much smaller than the reference density itself ($\rho' \ll \rho_0$).
* **Incompressibility:** The flow is assumed to be incompressible, meaning that the divergence of the velocity field is zero ($\nabla \cdot \mathbf{u} = 0$). This **effectively filters out sound waves**, which are associated with the compressibility of air.
* **Shallow Vertical Motions:** This approximation is most valid for atmospheric phenomena where the vertical scale of motion is much smaller than the scale height of the atmosphere (approximately 8-10 km). The scale height is the vertical distance over which the atmospheric density changes by a factor of *e* (approximately 2.718).

#### Applications and Limitations:

The Boussinesq approximation is well-suited for modeling **shallow convection**, such as the formation of fair-weather cumulus clouds, boundary layer thermals, and some types of thunderstorms where the vertical extent is limited.

However, its primary limitation is its inapplicability to **deep atmospheric phenomena**. For motions that span a significant portion of the troposphere, such as deep convective storms or large-scale atmospheric waves, **the assumption of a constant reference density breaks down**. As an air parcel rises through a deep layer of the atmosphere, the ambient pressure and density decrease significantly, a factor the Boussinesq approximation cannot adequately capture.

---

### The Anelastic Approximation: Accounting for a Stratified Atmosphere

The **anelastic approximation** offers a more refined approach by acknowledging that atmospheric density varies significantly with height. Instead of assuming a constant reference density, it considers a background density that decreases exponentially with altitude, which is a more realistic representation of the atmosphere's basic state.

#### Key Assumptions:

* **Small Perturbations from a Stratified Reference State:** Density and pressure are considered as small perturbations from a hydrostatically balanced reference state that varies with height ($\rho_0(z)$ and $p_0(z)$).
* **Filtering of Sound Waves:** Like the Boussinesq approximation, the anelastic approximation filters out sound waves. It achieves this by modifying the continuity equation to $\nabla \cdot (\rho_0 \mathbf{u}) = 0$. This allows for the expansion and compression of air parcels as they move vertically through the stratified atmosphere, but it does so in a way that does not support the propagation of acoustic waves.
* **Low Mach Number:** The fluid velocities are assumed to be much smaller than the speed of sound.

#### Applications and Limitations:

The anelastic approximation has a broader range of applications than the Boussinesq approximation. It is the foundation for many numerical models used to simulate **deep convection**, including severe thunderstorms and mesoscale convective systems. It is also employed in modeling larger-scale phenomena like mountain waves and atmospheric tides.

While more versatile, the anelastic approximation is more complex to implement than the Boussinesq approximation. **Its primary limitation is that it still filters out sound waves**, which, while often negligible, can be important in specific atmospheric contexts, such as the initial stages of explosive volcanic eruptions or certain types of atmospheric gravity waves.

---

### A Tale of Two Approximations: Boussinesq vs. Anelastic

| Feature | Boussinesq Approximation | Anelastic Approximation |
| :--- | :--- | :--- |
| **Reference Density** | Assumed to be a constant ($\rho_0$). | Varies with height ($\rho_0(z)$). |
| **Continuity Equation** | $\nabla \cdot \mathbf{u} = 0$ (Incompressible) | $\nabla \cdot (\rho_0 \mathbf{u}) = 0$ |
| **Vertical Scale** | Valid for shallow motions (much less than the scale height). | Valid for deep motions (comparable to the scale height). |
| **Complexity** | Simpler to implement. | More complex to implement. |
| **Typical Applications** | Shallow cumulus clouds, boundary layer thermals. | Deep convective storms, mountain waves. |

In essence, the Boussinesq approximation can be seen as a special case of the anelastic approximation where the atmospheric density is assumed to be uniform. The choice between the two depends on the specific atmospheric phenomenon being studied. For shallow systems where density variations with height are negligible, the simplicity of the Boussinesq approximation is advantageous. However, for deep atmospheric motions where the background density stratification plays a crucial role, the more physically realistic anelastic approximation is the necessary choice. These powerful tools, by simplifying the complexities of atmospheric motion, have become indispensable in our ongoing efforts to unravel the workings of the sky.

# AI source

From Google Gemini 2.5 Pro

# References

1. [Xu, L., S. Raman, and R. V. Madala. "A review of non-hydrostatic numerical models for the atmosphere." 1st World Congress of Nonlinear Analysis Fire and Forest Meteorology. Nonlinear World, Walter de Gruyter, New York, Tampa, FL. 1992.](https://sraman.wordpress.ncsu.edu/files/2020/09/J98.pdf)
2. [Introduction to Boussinesq and anelastic approximations for the atmosphere | MIT](https://pog.mit.edu/src/810/anelastic_boussinesq_intro.pdf)
3. [Durran, D. R., 1989: Improving the Anelastic Approximation. J. Atmos. Sci., 46, 1453–1461, https://doi.org/10.1175/1520-0469(1989)046<1453:ITAA>2.0.CO;2.](https://journals.ametsoc.org/view/journals/atsc/46/11/1520-0469_1989_046_1453_itaa_2_0_co_2.xml)