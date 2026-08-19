---
layout: post
title: MPAS | MPAS UCM | Urban Canopy Model | HKUST
categories: [MPAS]
tags: [MPAS, NWP, WRF]
author: wpsze
date: 2026-08-18 04:27:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/E6zeJnR.png
banner_img: https://i.imgur.com/E6zeJnR.png
---

- [MPAS | Convert LCZs into static.nc](https://waipangsze.github.io/2025/03/11/MPAS-Convert-LCZs-into-static-nc/)
- [WRF | MPAS | UCM Urban canopy parameters (UCPs)](https://waipangsze.github.io/2025/02/18/WRF-MPAS-UCM-Urban-canopy-parameters-UCPs/)
- [WRF | MPAS | UCM and LCZs](https://waipangsze.github.io/2025/01/27/WRF-MPAS-UCM-and-LCZs/)
- [Map of Local Climate Zones (LCZs)](https://waipangsze.github.io/2024/10/07/Global-LCZ-for-Urban/)

---

# MPAS workshop 2025

- Developing and Evaluating the MPAS-Urban Modeling System
  - <https://www.mmm.ucar.edu/events/133265/agenda>
  - <https://www2.mmm.ucar.edu/wrf/users/workshops/WS2025/presentations/5_Cao.pdf>


# Thesis Examination

- Developing and Evaluating the New MPAS-Urban Modeling System (Post Date: 29 June 2026)
  - <https://www.envr.ust.hk/events-and-media/events-and-seminars/event-b132b4329c51d7c562c2c9f0f997423c.html#2026-7>

# Papers 

Toward high-resolution urban modeling with MPAS-Urban: System development and sub-kilometer application for record-breaking 2022 Hong Kong Heatwave" from HKUST.

Yeer Cao, Zhuo Liu, Wanliang Zhang, et al. Toward high-resolution urban modeling with MPAS-Urban: System development and sub-kilometer application for record-breaking 2022 Hong Kong Heatwave. ESS Open Archive. 06 August 2026.
DOI: https://doi.org/10.22541/essoar.15007108/v1

- <https://essopenarchive.org/doi/abs/10.22541/essoar.15007108/v1>

## github

- <https://github.com/HKUST-MPAS/HKUST-MPAS>
- commit differences
  - <https://github.com/HKUST-MPAS/HKUST-MPAS/commit/210889d157cc3698369ddbe09e57ccaca4cae49a>

## highlight

{% note primary %}
**The main purposes of SLUCM are to parameterize the effects of the urban canopy layer and to calculate the exchanges of energy and momentum between the urban surface and the atmosphere using amore realistic geometry than slab models.**
{% endnote %}

- We therefore generated a **30 km–500 m** variable-resolution meshcentered on Hong Kong. 
  - The mesh was generated using the **Python-basedtoolkit MPAS_tool (Asay-Davis et al., 2026)** together with the **meshing package jigsaw_py(Engwirda, 2020)**. 
- The transparent region outside the solidlines indicates the buffer zone, where the **resolution changes gradually at a rate of 1/12km/degree** to ensure grid smoothness.
- Coupling single layer urban canopy model **to MPAS/Noah-MP**
- we develop a hybrid land-use dataset by combining the 100 m CGLC–LCZ product with **the default 1-km MODIS dataset**.
- implemented a **100-m global LCZ dataset (CGLC-LCZ)** specifically for the WRF-Urban modelingframework.
- Experiment setup:
  - All lower boundary conditions in the SLUCM scheme were set to **constant mode (BOUNDR =BOUNDB = BOUNDG = 2)**, This configuration allows the building elements to act as either heatsinks or sources during the simulations.
  - The temperature solution scheme was configured as **TS_SCHEME=2 (force–restore method)** to ensure urban surface energy balance.
  - **Anthropogenic sensible and latent heat fluxes** were both activated, using prescribed diurnal profiles.

### Initialization Notes

When creating initial conditions with `init_atmosphere_model`, users should ensure that the first atmospheric model level is high enough for the urban canopy parameters used in the simulation.

{% note primary %}
An example vertical level file (56 levels, first level at ~76 m, model top at 30 km) is provided in:
- vertical_levels/urban_ZR_75.txt
{% endnote %}

- vertical_levels/urban_ZR_75.txt

```sh
0.00000 
75.9370 
106.240 
178.373 
263.801 
......
```

- URBPARM_LCZ.TBL

```sh
......
#
#  Where there are multiple columns of values, the values refer, in
#  order, to: 1) Commercial, 2) High intensity residential, and 3) Low
#  intensity residential:  I.e.:
#
#  Index:         1              2             3            4          5          6               7                8              9             10         11
#  Type:  Comp High-Rise, Comp Mid-Rise, Comp Low-Rise, Op H-Rise, Op M-Rise, Op L-Rise, Lightweight L-Rise, Large L-Rise, Sparsely Built, Heavy Indus  Asphalt

#
# ZR:  Roof level (building height)  [ m ]
#      (sf_urban_physics=1)

ZR: 37.5, 17.5, 6.5, 37.5, 17.5, 6.5, 3., 6.5, 6.5, 10., 10.
......
```

{% note primary %}
Warning ZR : Mean Height Table + 2 m is larger than the 1st WRF level
{% endnote %}

- ???
  - if `building height = 37.5 m` and then `first layer height = (37.5+2)*2 = 79 m`
  - if `first layer = 75.9370 m`  and then `75.9370/2 = 37.9685 m`