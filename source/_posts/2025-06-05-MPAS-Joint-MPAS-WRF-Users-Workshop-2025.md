---
layout: post
title: MPAS | Joint MPAS/WRF Users Workshop 2025 | MPASv8.3.0
categories: [MPAS]
tags: [MPAS, NWP, WRF, ERA5, GFS]
author: wpsze
date: 2025-06-05 09:27:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/AJLIUh8.png
banner_img: https://i.imgur.com/AJLIUh8.png
---

- [Joint MPAS/WRF Users Workshop 2025 | 3-6 June 2025](https://www.mmm.ucar.edu/events/133265/agenda)
- [**MPAS-Atmosphere Users' Guide V8.3.0**](https://www2.mmm.ucar.edu/projects/mpas/mpas_atmosphere_users_guide_8.3.0.pdf)
- [**MPAS-A Technical Description**](https://www2.mmm.ucar.edu/projects/mpas/mpas_website_linked_files/MPAS-A_tech_note.pdf)

# Links

1. [https://mpas-dev.github.io/](https://mpas-dev.github.io/)
2. [/MPAS-Model/releases](https://github.com/MPAS-Dev/MPAS-Model/releases)
3. [MPAS-Atmosphere Users' Guide V4.0](https://www2.mmm.ucar.edu/projects/mpas/mpas_atmosphere_users_guide_4.0.pdf)
4. [MPAS-Atmosphere Users' Guide V5.3](https://www2.mmm.ucar.edu/projects/mpas/mpas_atmosphere_users_guide_5.3.pdf)
5. [MPAS-Atmosphere Users' Guide V6.3](https://www2.mmm.ucar.edu/projects/mpas/mpas_atmosphere_users_guide_6.3.pdf)
6. [MPAS-Atmosphere Users' Guide V7.0](https://www2.mmm.ucar.edu/projects/mpas/mpas_atmosphere_users_guide_7.0.pdf)
7. [MPAS-Atmosphere Users' Guide V8.2.0](https://www2.mmm.ucar.edu/projects/mpas/mpas_atmosphere_users_guide_8.2.0.pdf)
8. [Joint WRF/MPAS Users Workshop 2024](https://www.mmm.ucar.edu/events/133129/agenda)

# Workshop

MPAS V8.2.0 – 27 June 2024\
MPAS V8.2.1 – 7 August 2024\
MPAS V8.2.2 – 20 September 2024\
MPAS V8.2.3 – 22 May 2025\
**MPAS V8.3.0 – 2 June 2025**
---


## About MPAS
{% gi 5 2-3 %}
![](https://i.imgur.com/BsAqBAV.png)
![](https://i.imgur.com/AJLIUh8.png)
![](https://i.imgur.com/9Oymso8.png)
![](https://i.imgur.com/DztjZIT.png)
![](https://i.imgur.com/Eq9Uem9.png)
{% endgi %}

## About WRF

{% gi 2 2 %}
![](https://i.imgur.com/IVC9kF4.png)
![](https://i.imgur.com/7DxkjNQ.png)
{% endgi %}

## About MPAS-JEDI

{% gi 4 2-2 %}
![](https://i.imgur.com/xqR9lSN.png)
![](https://i.imgur.com/054SC0D.png)
![](https://i.imgur.com/g6z8JCP.png)
![](https://i.imgur.com/vSKRVAz.png)
{% endgi %}

## MPAS-LES

Dudhia: MPAS-LES

1) LES physics is basically three-dimensional turbulence suitable for grids where dx ~ dz
2) Testing SAS Idealized Case
   1) 15-meter grid size - 252720 columns
   2) Top at 3 km (200 levels)
3) Testing on MPAS idealized supercell at 167 m grid size
4) Main code is in new module dynamics/mpas_atm_dissipation_models.F
5) Activated with config_les_model = 'prognostic_1.5_order’
6) Current code is a branch updated to MPAS V8.2.2
7) The plan is to have it in at least a released branch this year

## MPAS-UCM 

HKUST: MPAS-UCM

1) mesh: 30km-500m
2) generate user defined arbitrary mesh based on geojson file
3) Coupling single UCM in MPAS add LCZ in MPAS
