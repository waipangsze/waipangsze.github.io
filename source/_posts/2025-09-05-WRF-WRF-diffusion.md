---
layout: post
title: WRF | WRF diffusion
categories: [WRF]
tags: [WRF, MPAS, NWP, diffusion]
author: wpsze
date: 2025-09-05 12:11:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

# WRF diffusion

WRF (Weather Research and Forecasting model) diffusion refers to the various methods used within the model to represent the mixing of atmospheric properties and to control numerical noise, including implicit diffusion within advection schemes and explicit diffusion schemes added to control noise. It also encompasses subgrid-scale diffusion that represents turbulent mixing, which can be controlled by the physics parameterization schemes, such as the `PBL scheme` and the boundary layer schemes, and configured using options like `km_opt` in the namelist. 

## Types of Diffusion in WRF

1. **Implicit Diffusion**:
   1. **Source**: This type of diffusion is inherent in the odd-ordered, upwind-biased advection schemes (like the fifth-order schemes) used in the WRF model. 
   2. **Function**: It acts as a form of scale-selective diffusion to prevent excessive noise growth in simulated fields. 
   3. **Limitation**: In some conditions, especially with low grid-relative wind speeds and nearly neutral or unstable stratification, this inherent diffusion can be insufficient to remove noise, leading to noise competing with physical phenomena. 
2. **Explicit Numerical Diffusion**:
   1. **Purpose**: Added to the WRF model to explicitly reduce noise that isn't adequately handled by the inherent diffusion. 
   2. **Characteristics**: A common implementation is a **sixth-order, monotonic scheme that uses a flux limiter** to ensure accuracy. 
   3. **Benefit**: It helps maintain the high effective resolution of the WRF model while providing better control over numerical artifacts. 
3. **Subgrid-Scale (Turbulent) Diffusion**:
   1. **Purpose**: Represents the physical process of turbulence and mixing in the atmosphere that occurs at scales smaller than the model's grid resolution. 
   2. **Parameterization**: This is handled by the WRF physics parameterization schemes, particularly the **Boundary Layer (PBL) scheme** and other boundary layer schemes. 
   3. **Configuration**: Users can configure this through the `km_opt` option in the namelist.input file, choosing between constant coefficients, coefficients dependent on turbulence kinetic energy (TKE), or a Smagorinsky-like scheme. 

### Reading

- [The utility of 6th-order, monotonic, numerical diffusion in the Advanced Research WRF Model](https://www2.mmm.ucar.edu/wrf/users/workshops/WS2005/abstracts/Session3/15-Knievel.pdf)
  - The coefficient in this diffusive term is proportional to the speed of the advecting wind, so in light wind the diffusion is weak. It turns out that in some cases the diffusion is much too weak to filter poorly resolved kinematical features with wavelengths of 2–4 times the grid interval. These poorly resolved features can grow until they dominate fields of horizontal divergence and vertical velocity in the daytime boundary layer
- [Knievel, J. C., G. H. Bryan, and J. P. Hacker, 2007: Explicit Numerical Diffusion in the WRF Model. Mon. Wea. Rev., 135, 3808–3824, https://doi.org/10.1175/2007MWR2100.1.](https://journals.ametsoc.org/view/journals/mwre/135/11/2007mwr2100.1.xml)
  - One solution to this problem is an explicit, sixth-order numerical diffusion scheme that preserves the WRF model’s high effective resolution and uses a flux limiter to ensure monotonicity. The scheme, and how it was added to the WRF model, are explained. The scheme is then demonstrated in an idealized framework and in simulations of salt breezes and lake breezes in northwestern Utah.
- [Diffusion Options ](https://www2.mmm.ucar.edu/wrf/users/wrf_users_guide/build/html/dynamics.html#diffusion-options-diff-opt)
  - (diff_opt)
  - K Options (km_opt)
    - ![](https://i.imgur.com/NsOdFDs.png)

## When Diffusion is Important

1. **Numerical Stability**: Implicit and explicit diffusion are crucial for maintaining numerical stability and preventing the amplification of noise in the model's dynamical core. 
2. **Physical Processes**: Subgrid-scale diffusion is vital for accurately simulating turbulent processes, heat and moisture transport, and mixing in the atmospheric boundary layer. 
3. **Model Development**: The concept of diffusion is fundamental to the design and modification of WRF, with researchers adding explicit schemes to improve simulation quality. 

# The 2D Smagorinsky model

- [Smagorinsky, J. (1963). General circulation experiments with the primitive equations: I. The basic experiment. Monthly weather review, 91(3), 99-164.](https://journals.ametsoc.org/view/journals/mwre/91/3/1520-0493_1963_091_0099_gcewtp_2_3_co_2.xml)
- Smagorinsky, J. (1993). Some historical remarks on the use of nonlinear viscosities. Large eddy simulation of complex engineering and geophysical flows, 1, 69-106.
- [Zhang, X., Huang, Q., & Ma, Y. (2024). A dynamic Smagorinsky model for horizontal turbulence parameterization in tropical cyclone simulation. Geophysical Research Letters, 51(19), e2024GL110392.](https://agupubs.onlinelibrary.wiley.com/doi/pdfdirect/10.1029/2024GL110392)
- Zhang, X., Zhou, B., & Ping, F. (2021). Effects of subgrid-scale horizontal turbulent mixing on a simulated convective storm at kilometer-scale resolutions. Atmospheric Research, 254, 105445.

1. Note that aside from the **1D PBL plus 2D Smagorinsky turbulence** package, several recent studies have also advocated the **extension of LES turbulence closures** to kilometer-scale resolution simulations
   1. ![](https://i.imgur.com/lo8hEOx.png)

# A Three-Dimensional Scale-Adaptive Turbulent Kinetic Energy Scheme in the WRF-ARW Model

- [A Three-Dimensional Scale-Adaptive Turbulent Kinetic Energy Scheme in the WRF-ARW Model](https://repository.library.noaa.gov/view/noaa/21975/noaa_21975_DS1.pdf)
  - a. LES limit 
  - b. Mesoscale limit
  - {% gi 2 2 %}
    ![](https://i.imgur.com/0n6XOTB.png)
    ![](https://i.imgur.com/D9FogiC.png)
    {% endgi %}

# Examples

## WRF Diffusion in Complex TerrainDealing with Overmixing Near Steep Terrain

- [WRF Diffusion in Complex TerrainDealing with Overmixing Near Steep Terrain | 2017](https://www2.mmm.ucar.edu/wrf/users/workshops/WS2017/oral_presentations/8.3.pdf)
  - The University of Washington real-time modeling system included second order diffusion along modelsurfaces and 6th order diffusion
    - change to second order diffusion
    - elimilate 6th order diffusion
    - To reduce the mixing
  - {% gi 8 2-2-2-2 %}
    ![](https://i.imgur.com/NuQZkD0.png)
    ![](https://i.imgur.com/QmPTTNz.png)
    ![](https://i.imgur.com/y6Y0QJe.png)
    ![](https://i.imgur.com/vLKS2dE.png)
    ![](https://i.imgur.com/MdPSCs0.png)
    ![](https://i.imgur.com/ViHoT0Q.png)
    ![](https://i.imgur.com/SRsUeH5.png)
    ![](https://i.imgur.com/RV07Y3Q.png)
    {% endgi %}

## WRF Numerical Diffusion 效能評估及個案模擬與分析

- [WRF Numerical Diffusion 效能評估及個案模擬與分析 | 2010](https://photino.cwa.gov.tw/rdcweb/lib/cd/cd01conf/dissertation/2010/029.pdf)
  - ![](https://i.imgur.com/VXFITNt.png)