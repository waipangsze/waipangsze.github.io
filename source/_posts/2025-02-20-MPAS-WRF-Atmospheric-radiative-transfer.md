---
layout: post
title: MPAS | WRF | Atmospheric radiative transfer
categories: [MPAS]
tags: [MPAS, NWP, WRF, ERA5, GFS, RRTM]
author: wpsze
date: 2025-02-20 11:26:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

# Radiative transfer

**Radiative transfer** (also called **radiation transport**) is the physical phenomenon of energy transfer in the form of electromagnetic radiation. The propagation of radiation through a medium is affected by *absorption, emission, and scattering processes*. The equation of radiative transfer describes these interactions mathematically.

## Intensity

In physics and many other areas of science and engineering the **intensity** or **flux of radiant energy** is the *power* transferred per *unit area*, where the area is measured on the plane perpendicular to the direction of propagation of the energy. In the SI system, it has units watts per square metre (**W/m2**).

If a point source is radiating energy in all directions (producing a spherical wave), and no energy is absorbed or scattered by the medium, then the intensity decreases in proportion to the distance from the object squared. This is an example of the inverse-square law.

Applying the law of conservation of energy, if the net power emanating is constant,

$$
P = \int \mathbf{I} \cdot d\mathbf{A},
$$

where

- $P$ is the net power (Watts) radiated;
- $\mathbf{I}$ is the intensity vector as a function of position;
- the magnitude $|\mathbf{I}|$ is the intensity as a function of position;
- $d\mathbf{A}$ is a differential element of a closed surface that contains the source.

## Intensity as a Function of Position on Earth’s Surface

### **Understand the Power Output of the Sun (Luminosity)**

The total power (or luminosity) of the Sun, $P_{\text{Sun}}$ , represents the total energy it emits per unit time across all wavelengths and directions. This is a well-known value in astrophysics, and as of my latest knowledge update, the Sun’s luminosity is approximately:

$$
\begin{align}
P/A &= \sigma T^4 \\
\sigma &= 5.67×10^{-8}\\
R_{\text{Sun}} &= 695500×1000 m \\
T_{\text{Sun}} &= 5778 K \\
P_{\text{Sun}} &\approx 3.828 \times 10^{26} \, \text{W} \, (\text{watts})
\end{align}
\tag{1}
$$

This is the net power radiated by the Sun into space, assuming it behaves as a **near-perfect blackbody emitter** at a *surface temperature* of about $5,778 K$.

### Earth’s Distance from the Sun:

The average distance from the Sun to Earth is:

$$
r \approx 1 \, \text{AU} = 1.496 \times 10^{11} \, \text{m}
$$

Now, calculate $r^2$:

$$
r^2 = (1.496 \times 10^{11})^2 = 2.238 \times 10^{22} \, \text{m}^2
$$

Calculate $4 \pi r^2$:

$$
4 \pi r^2 \approx 4 \times 3.1416 \times 2.238 \times 10^{22} \approx 2.813 \times 10^{23} \, \text{m}^2
$$

Now, compute the intensity at Earth’s distance (top of the atmosphere, assuming no atmospheric effects):

$$
I = \frac{3.828 \times 10^{26}}{2.813 \times 10^{23}} \approx 1,361 \, \text{W/m}^2
$$

### **Geometry of Earth’s Surface**

Earth is a sphere with a radius $R_{\text{Earth}} \approx 6,371 \, \text{km} = 6.371 \times 10^6 \, \text{m}$. The intensity (flux) at the top of the atmosphere $(1,361 W/m²)$ is the incident flux perpendicular to the Sun’s rays. However, on Earth’s surface, the intensity varies due to:

- The angle of incidence (cosine effect): The flux decreases as $\cos\theta$, where $\theta$ is the zenith angle (angle from the vertical).
- The curvature of Earth: Only a portion of Earth’s surface is illuminated at any time, and the flux spreads over the illuminated hemisphere.

### **Assuming Perpendicular Incidence at the Sub-Solar Point**

At the sub-solar point (directly under the Sun, where $\theta = 0$), the intensity on Earth’s surface (ignoring atmospheric effects, as per the image) would still be $1,361 W/m²$, because no energy is absorbed or scattered.

Across the rest of the illuminated hemisphere, the intensity decreases with $\cos\theta$. For a point on Earth’s surface at zenith angle $\theta$, the flux is:

$$
I_{\text{surface}} = I \cdot \cos\theta = 1,361 \cdot \cos\theta \, \text{W/m}^2
$$

The differential area element on a sphere is $dA = R_{\text{Earth}}^2 \sin\theta \, d\theta \, d\phi$, where $\theta$ is the zenith angle (0 to $\pi/2$ for the illuminated hemisphere) and $\phi$ is the azimuthal angle (0 to $2\pi$).

The intensity at angle $\theta$ is $I \cdot \cos\theta$. The power $dP$ through a differential area $dA$ is:

$$
dP = I \cdot \cos\theta \cdot dA = (1,361 \cdot \cos\theta) \cdot (R_{\text{Earth}}^2 \sin\theta \, d\theta \, d\phi)
$$

Integrate over $\theta$ from 0 to $\pi/2$ and $\phi$ from 0 to $2\pi$:

$$
P_{\text{Earth}} = \int_0^{2\pi} \int_0^{\pi/2} (1,361 \cdot \cos\theta) \cdot (R_{\text{Earth}}^2 \sin\theta) \, d\theta \, d\phi
$$

First, integrate over $\phi$:

$$
\int_0^{2\pi} d\phi = 2\pi
$$

Now, integrate over $\theta$:

$$
\int_0^{\pi/2} (1,361 \cdot \cos\theta) \cdot (\sin\theta) \, d\theta = 1,361 \cdot R_{\text{Earth}}^2 \int_0^{\pi/2} \sin\theta \cos\theta \, d\theta
$$

Use the substitution $u = \sin\theta$, so $du = \cos\theta \, d\theta$. The limits change from $\theta = 0$ to $\pi/2$ (i.e., $u = 0$ to 1):

$$
\int_0^{\pi/2} \sin\theta \cos\theta \, d\theta = \int_0^1 u \, du = \left[ \frac{u^2}{2} \right]_0^1 = \frac{1}{2}
$$

So:

$$
P_{\text{Earth}} = 1,361 \cdot R_{\text{Earth}}^2 \cdot 2\pi \cdot \frac{1}{2} = 1,361 \cdot \pi \cdot R_{\text{Earth}}^2
$$

Substitute $R_{\text{Earth}} = 6.371 \times 10^6 \, \text{m}$:

$$
R_{\text{Earth}}^2 = (6.371 \times 10^6)^2 = 4.059 \times 10^{13} \, \text{m}^2
$$

$$
P_{\text{Earth}} = 1,361 \cdot \pi \cdot 4.059 \times 10^{13}
$$

$$
P_{\text{Earth}} \approx 4,276 \cdot 4.059 \times 10^{13} \approx 1.736 \times 10^{17} \, \text{W}
$$

This is the total power received by Earth’s illuminated hemisphere, ignoring atmospheric effects. However, note that the actual power received at the surface is less due to atmospheric absorption and scattering.

#

## Solutions to the equation of radiative transfer

Solutions to the equation of radiative transfer form an enormous body of work. The differences however, are essentially due to the various forms for the emission and absorption coefficients. If scattering is ignored, then a general steady state solution in terms of the emission and absorption coefficients may be written:

$$
I_\nu(s)=I_\nu(s_0)e^{-\tau_\nu(s_0,s)}+\int_{s_0}^s j_\nu(s')
e^{-\tau_\nu(s',s)}\,ds'
$$

where $\tau_\nu(s_1,s_2)$ is the **optical depth** of the medium between positions $s_1$ and $s_2$:

$$
\tau_\nu(s_1,s_2) \ \stackrel{\mathrm{def}}{=}\ \int_{s_1}^{s_2} \alpha_\nu(s)\,ds
$$

# References

1. [Atmospheric radiative transfer codes](https://en.wikipedia.org/wiki/Atmospheric_radiative_transfer_codes)
2. [lecture.2.global.pdf](https://www.ess.uci.edu/~yu/class/ess200a/lecture.2.global.pdf)
3. [Radiation Fundamentals](https://www.cv.nrao.edu/~sransom/web/Ch2.html)
4. [Chapman, I. M., Naylor, D. A., Gom, B., Querel, R. R., & Davis-Imhof, P. (2010). Btram: An interactive atmospheric radiative transfer model. In 30th Canadian Symposium.](https://www.ulethbridge.ca/phy/naylor/documents/pdf/btram.pdf)
