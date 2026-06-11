---
layout: post
title: NWP | Derivation of Turbulent Kinetic Energy (TKE) from YSU/SH 
categories: [NWP]
tags: [NWP, MPAS, WRF, PBL, YSU, SH, TKE, EDR, aviation]
author: wpsze
date: 2026-06-11 06:30:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/NtQb7zF.png
banner_img: https://i.imgur.com/NtQb7zF.png
---

- [NWP | Eddy Dissipation Rate (EDR)](https://waipangsze.github.io/2025/04/08/Eddy-Dissipation-Rate-EDR/)
- [MPAS | Eddy dissipation rate (EDR)](https://waipangsze.github.io/2025/11/10/MPAS-Eddy-dissipation-rate-EDR/)
  - We developed a method to estimate the eddy dissipation rate (EDR) using the **resolved wind field of the MPAS simulations**. (3D field)
  - **EDR at the altitude of 10km** 

---

# Turbulent Kinetic Energy

# Derivation of TKE from YSU

- [Shin, H. H., S. Hong, Y. Noh, and J. Dudhia, 2013: **Derivation of Turbulent Kinetic Energy from a First-Order Nonlocal Planetary Boundary Layer Parameterization**. J. Atmos. Sci., 70, 1795–1805, https://doi.org/10.1175/JAS-D-12-0150.1.](https://journals.ametsoc.org/view/journals/atsc/70/6/jas-d-12-0150.1.xml)

**Turbulent kinetic energy (TKE)** is derived from a first-order planetary boundary layer (PBL) parameterization for convective boundary layers: the nonlocal K-profile Yonsei University (YSU) PBL. A parameterization for the TKE equation is developed to calculate TKE based on meteorological profiles given by the YSU PBL model. For this purpose buoyancy- and shear-generation terms are formulated consistently with the YSU scheme—that is, the combination of local, nonlocal, and explicit entrainment fluxes. The vertical transport term is also formulated in a similar fashion. A length scale consistent with the K profile is suggested for parameterization of dissipation.

- **A parameterization for TKE equation is developed to calculate TKE based on meteorological profiles of wind, temperature, and moisture from the YSU PBL model**. For this purpose, buoyancy- and shear-generation terms are formulated by the combination of local, nonlocal, and explicit entrainment fluxes, consistent with the YSU scheme. The vertical transport term is also formulated in a similar fashion. To calculate dissipation length scale and parameterize dissipation, a master length scale consistent with the K profile is introduced. **Note that this development is for convective boundary layers.**
- The **introduced TKE calculationis a one-way method such that calculated TKE is sensitive to the length scale**,given the same mean meteorological fields.
- Note that in the TKE budget profiles, **the TKE budgets at the lower boundary are excluded**. This is because TKE is directly imposed by the lower boundary condition, rather than calculated by solving the TKE equation. Therefore, the underestimated near-surface TKE cannot be inferred from the TKE budgets.

Remarks:

- **A convective boundary layer** is the part of the **lower atmosphere that becomes turbulent and well mixed** because the surface heats the air from below, making warmer air rise and cooler air sink.
- No — not in the sense the paper defines it. The TKE in this study is a boundary-layer diagnostic built for convective boundary layers, so it is meant for the mixed layer and upper part of the PBL, not for free-atmospheric levels like 850 hPa or 10 km.
  - The paper’s formulation is based on YSU PBL physics, which parameterizes turbulence within the planetary boundary layer, not the deep free atmosphere.
  - The authors explicitly say the development is for convective boundary layers, and their tests are the GABLS2 boundary-layer case, whose top is around 1 km, not 10 km.
  - They also note that the lowest boundary TKE is imposed rather than prognosed, so the scheme is not designed as a general atmospheric TKE field.

# WRF github

- <https://github.com/NCAR/MMM-physics/blob/main/bl_shinhong.F90>


## For first level, it is specific, 

- Note that in the TKE budget profiles, **the TKE budgets at the lower boundary are excluded**. This is because TKE is directly imposed by the lower boundary condition, rather than calculated by solving the TKE equation. Therefore, the underestimated near-surface TKE cannot be inferred from the TKE budgets.

{% note primary %}
This is because TKE is directly imposed by the lower boundary condition, rather than calculated by solving the TKE equation.
{% endnote %}

```fortran
   real(kind=kind_phys),     dimension( kts:kte )                            , &
             intent(inout)   ::                                            q2

   real(kind=kind_phys),parameter :: epsq2l = 0.01,c0 = 0.55,ceps = 16.6,g =9.81

   rc02=2.0/(c0*c0)

!
!  lower boundary condition for q2
!
   q2(kts)=max(rc02*ustar*ustar,epsq2l)
```

![](https://i.imgur.com/Ex26KzN.png)

- `rc02=6.6116` and `ustart` is the friction velocity scale.
- [Witek, M. L., J. Teixeira, and G. Matheou, 2011: An Integrated TKE-Based Eddy Diffusivity/Mass Flux Boundary Layer Closure for the Dry Convective Boundary Layer. J. Atmos. Sci., 68, 1526–1540, https://doi.org/10.1175/2011JAS3548.1.](https://journals.ametsoc.org/view/journals/atsc/68/7/2011jas3548.1.xml)