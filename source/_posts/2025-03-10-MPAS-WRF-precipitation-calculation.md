---
layout: post
title: MPAS | WRF | precipitation calculation
categories: [MPAS]
tags: [MPAS, NWP, WRF, ERA5, GFS, rainc, rainnc]
author: wpsze
date: 2025-03-10 11:40:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

# Precipitation variables

- **rainc**: accumulated total cumulus precipitation
- **rainsh**: accumulated shallow cumulus precipitation
- **rainnc**: accumulated total grid scale precipitation
- **snownc**: accumulated total grid scale snow and ice
- **graupelnc**: accumulated total grid scale graupel
- **hailnc**: accumulated total grid scale hail

{% note primary %}
- `rainc`:		rainfall from a cumulus scheme (mm) from **cu_physics/cumulus scheme precipitation**
- `rainnc`:		rainfall from an explicit scheme (mm) from **microphysics**
{% endnote %}

{% note primary %}
- **Convective** Precip ---> rain**c**
- **Non-convective** Precip ---> rain**nc**
{% endnote %}

- [WRF precipitation calculation | Apr 4, 2023](https://forum.mmm.ucar.edu/threads/wrf-precipitation-calculation.12798/#post-30917)
  - **Total accumulated precipitation is always "rainc + rainnc"**. Other items are included in rainc and rainnc calculation.
  - In fact, **shallow convection** produces little precipitation.
- [rainc, rainnc | 2024](https://forum.mmm.ucar.edu/threads/rainc-rainnc.16696/#post-41178)
  - **Rainc is cumulative convective precipitation**
  - **Rainnc is cumulative non convective precipitation**
- [wrf rainfall visualisation | 2024](https://forum.mmm.ucar.edu/threads/wrf-rainfall-visualisation.17086/#post-41902)
  - `rainc` and `rainnc` are **convective** and **large-scale precipitation**, respectively. 
  - Both are accumulated variables, which means they are accumulated from the beginning of the model run. Total precipitation is rainc + rainnc.
  
# cumulative variables

The variables `RAINC` and `RAINNC` are **cumulative variables**. To get the values at any particular point in time, you would need to calculate that. For e.g. if you are interested in **daily values** for 2017-11-05,

- RAINC = RAINC (at time 2017-11-06_00:00:00) - RAINC (at time 2017-11-05_00:00:00).


# Rainfall only (Liquid precipitation)

- phys/module_diag_trad_fields.F
  
```fortran
! Total rainfall
    rain(i,j) =   rainc(i,j) + rainnc(i,j)
    ! Total liquid rainfall
    liqrain(i,j) =   rainc(i,j) + rainnc(i,j) - snownc(i,j) - graupelnc(i,j) - hailnc(i,j)
```