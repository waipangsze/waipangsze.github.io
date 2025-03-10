---
layout: post
title: "Relative Humidity and the Dewpoint Temperature"
categories: [Meteorology]
tags: [NWP, MPAS, WRF]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-03-06 10:43:00
index_img: https://i.imgur.com/N0hv8M5.png
banner_img: https://i.imgur.com/N0hv8M5.png
---

Key words: Relative Humidity (rh), Dewpoint Temperature (dewpoint), Water Vapor Mixing Ratio (qv)

- [Bolton, D. (1980). The computation of equivalent potential temperature. Monthly weather review, 108(7), 1046-1053.](https://journals.ametsoc.org/view/journals/mwre/108/7/1520-0493_1980_108_1046_tcoept_2_0_co_2.xml)
- [Lawrence, M. G. (2005). The relationship between relative humidity and the dewpoint temperature in moist air: A simple conversion and applications. Bulletin of the American Meteorological Society, 86(2), 225-234.](https://journals.ametsoc.org/view/journals/bams/86/2/bams-86-2-225.xml)
  - How are the dewpoint temperature and relative humidity related, and is there an easy and sufficiently accurate way to convert between them without using a calculator?

# WRF

- [relative humidity | 2020](https://forum.mmm.ucar.edu/threads/relative-humidity.9134/)
  - The Relative Humidity (RH) is simply the mixing ratio divided by the saturation mixing ratio. wrfout files include mixing ratio and T, from which you can easily obtain RH.
  - Here is a small piece of Fortran to calculate relative humidity from mixing ratio:
    ```fortran
    real, parameter :: svp1=611.2
    real, parameter :: svp2=17.67
    real, parameter :: svp3=29.65
    real, parameter :: svpt0=273.15
    real, parameter :: eps = 0.622
    rh = 1.E2 * (p*q/(q*(1.-eps) + eps))/(svp1*exp(svp2*(t-svpt0)/(T-svp3)))
    ```
  - Note that **t (temperature), p (pressure) and q (mixing ratio)** are output variables in wrfout.
- [How to derive Specific Humidity from WRF output ? | 2024](https://forum.mmm.ucar.edu/threads/how-to-derive-specific-humidity-from-wrf-output.16403/)
  - You're right, that specific humidity is not a variable that is available to output with wrf. It looks like there is one (called QLEV_URB3D) if you happen to be using an urban parameterization scheme. Otherwise, you may have to calculate it during post-processing. I would recommend using either NCL or python to try to get the output you want.