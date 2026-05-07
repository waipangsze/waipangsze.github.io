---
layout: post
title: NWP | MPAS/WRF Physics
categories: [NWP]
tags: [MPAS, WRF, NWP, GFS, FNL, IFS, ERA5, CMA, ACC]
author: wpsze
date: 2026-05-07 06:30:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/Rvb39Ht.png
banner_img: https://i.imgur.com/Rvb39Ht.png
---

- [**WRF Physics**](https://www2.mmm.ucar.edu/wrf/users/wrf_users_guide/build/html/physics.html)
- [**201401/Physics_full.pdf**](https://www2.mmm.ucar.edu/wrf/users/tutorial/presentation_pdfs/201401/Physics_full.pdf)
  - P.105 - Physics in Multiscale NWP Model
  - P.106 - Solver Calling Sequence (ARWexample)
- [**Overview of WRFPhysics/201701/physics.pdf**](https://www2.mmm.ucar.edu/wrf/users/tutorial/presentation_pdfs/201701/physics.pdf)

---

The Weather Research and Forecasting (WRF) model executes physics schemes in a specific, ordered manner within its temporal loop to simulate atmospheric processes. Physics schemes are generally called after the dynamical solver calculates advection and pressure gradient forces to update the state of the atmosphere based on sub-grid scale processes.

{% gi 6 2-2-2 %}
![](https://i.imgur.com/jlRW5KB.png)
![](https://i.imgur.com/W17YfrJ.png)
![](https://i.imgur.com/HaMQRNq.png)
![](https://i.imgur.com/7JqeHLE.png)
![](https://i.imgur.com/pr9xueZ.png)
![](https://i.imgur.com/EbAlOUQ.png)
{% endgi %}