---
layout: post
title: NWP | Microphysics | Convert moisture variables q's into rainfall
categories: [NWP]
tags: [NWP, MPAS, WRF, Microphysics]
author: wpsze
date: 2026-07-02 06:30:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

Atmospheric models convert moisture variables ($q_v, q_c, q_r, q_i, q_s, q_g$) into rainfall and precipitation through microphysics parameterizations that simulate microphysical phase changes and droplet growth mechanics.  

## The Microphysical Variables Explained

* $q_v$ (Water Vapor Mixing Ratio): Gas-phase water molecules suspended in the air.
* $q_c$ (Cloud Water Mixing Ratio): Liquid droplets so small (<20 μm) they have negligible fall velocity.
* $q_r$ (Rain Water Mixing Ratio): Large liquid drops (>100 μm) heavy enough to fall to the surface.
* $q_i$ (Cloud Ice), $q_s$ (Snow), $q_g$ (Graupel): Solid-phase microphysical species (frozen precipitation). 

------------------------------
## The Conversion Pathway to Surface Precipitation

The conversion of vapor to surface rainfall follows a distinct chain of physical processes.

```log
       [ qv: Water Vapor ]
               │
               ▼ (Condensation / Deposition)
       [ qc: Cloud Water ] ──(Freezing)──> [ qi / qs / qg: Ice/Snow/Graupel ]
               │                                      │
               ▼ (Autoconversion / Accretion)         ▼ (Melting)
       [ qr: Rain Water ] <───────────────────────────┘
               │
               ▼ (Sedimentation / Fall out)
     [ Surface Precipitation ]
```

## 1. Condensation ($q_v \rightarrow q_c$)

When air becomes supersaturated (RH > 100%), excess water vapor ($q_v$) condenses onto cloud condensation nuclei (CCN) to form cloud liquid water ($q_c$). If temperatures are below freezing (T < 0°C), $q_v$ transforms directly into cloud ice ($q_i$) via vapor deposition. 

## 2. Autoconversion ($q_c \rightarrow q_r$) 

Cloud droplets ($q_c$) are too small to fall. As they collide and merge (coalescence), they grow in size. Once they cross a critical size threshold (typically around 20 μm in radius), they are mathematically reclassified from cloud water ($q_c$) to rainwater ($q_r$). 

## 3. Accretion ($q_c + q_r \rightarrow q_r$) 

Once rainwater ($q_r$) drops exist, they fall much faster than small cloud droplets ($q_c$). As a raindrop falls through a cloud layer, it sweeps up and absorbs the smaller cloud droplets in its path, rapidly increasing the rainwater volume ($q_r$). 

## 4. The Cold-Rain Pathway ($q_i \rightarrow q_s \rightarrow q_g \rightarrow q_r$)

In many mid-latitude and convective clouds, rain starts as ice: 

* Vapor deposits onto cloud ice ($q_i$), growing it into snow ($q_s$).
* Snow collects supercooled cloud water, freezing it instantly to form dense graupel ($q_g$).
* As $q_s$ and $q_g$ fall below the freezing level (T > 0°C), they melt into liquid rainwater ($q_r$). 

## 5. Sedimentation and Evaporation ($q_r \rightarrow \text{Rainfall}$)

Rainwater drops fall toward the ground due to gravity, a process called sedimentation. As they fall through sub-saturated air layers beneath the cloud base, a portion of the rainwater evaporates back into vapor ($q_r \rightarrow q_v$). The remaining rainwater mass that successfully strikes the ground is measured as surface precipitation. 

