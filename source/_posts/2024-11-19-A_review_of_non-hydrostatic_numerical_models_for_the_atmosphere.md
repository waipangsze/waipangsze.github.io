---
layout: post
title: "NWP | Non-hydrostatic numerical models for the atmosphere"
categories: [NWP]
tags: [WRF,MPAS,ECMWF, paper]
author: wpsze
date: 2024-11-19 10:22:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

# A review of non-hydrostatic numerical models for the atmosphere

{% note primary %}
**Xu, L., Raman, S., & Madala, R. V. (1992, August). A review of non-hydrostatic numerical models for the atmosphere. In 1st World Congress of Nonlinear Analysis Fire and Forest Meteorology. Nonlinear World, Walter de Gruyter, New York, Tampa, FL.**
{% endnote %}

Three major components are discussed.

- Governing equations
  - Various approximations to governing equations
  - The Boussinesq anelastic and fully compressible treatments
  - Various vertical coordinate systems
- Numerical techniques
  - To handle acoustic and fast moving gravity modes
  - Including semi-implicit and split-explicit time integration schemes to control the acoustic modes
  - Method of defining vertical and lateral boundary conditions are also discussed
- Inclusion of nonlinear physical processes
  - Review inclusion of nonlinear physical processes


# References

1. [Steppeler, J., Hess, R., Schättler, U., & Bonaventura, L. (2003). Review of numerical methods for nonhydrostatic weather prediction models. Meteorology and Atmospheric Physics, 82, 287-301.](https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=34f8ad634c6a397280184c216e9ac25997358dce)
2. [Skamarock, William C., and Joseph B. Klemp. "The stability of time-split numerical methods for the hydrostatic and the nonhydrostatic elastic equations." Monthly Weather Review 120.9 (1992): 2109-2127.](https://journals.ametsoc.org/view/journals/mwre/120/9/1520-0493_1992_120_2109_tsotsn_2_0_co_2.pdf)
3. [Skamarock, William C. "proximate equations that filter modes that are computationally difficult to include. Examples." (1991)](https://www.ecmwf.int/sites/default/files/elibrary/1991/12300-numerical-methods-nonhydrostatic-models.pdf)