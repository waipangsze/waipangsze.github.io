---
layout: post
title: DA | VarBC
categories: [NWP]
tags: [NWP, MPAS, WRF, WRFDA, VarBC, DA, 3dVar, 4dVar, KF, EKF, EnKF]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-06-04 17:20:00
index_img: https://i.imgur.com/bo9h7ek.png
banner_img: https://i.imgur.com/bo9h7ek.png
---

{% note primary %}
Most data assimilation schemes assumes an **unbiased observation model error.**
{% endnote %}

- [Berry, Tyrus, and John Harlim. "Correcting biased observation model error in data assimilation." Monthly Weather Review 145.7 (2017): 2833-2853.](https://journals.ametsoc.org/view/journals/mwre/145/7/mwr-d-16-0428.1.xml)
  - While the formulation of **`most data assimilation schemes assumes an unbiased observation model error`**, 
  - **in real applications model error with nontrivial biases is unavoidable**. 
  - **A practical example is errors in the radiative transfer model** (which is used to assimilate satellite measurements) in the presence of clouds. Together with the dynamical model error, the result is that many (in fact 99%) of the cloudy observed measurements are not being used although they may contain useful information.
  - ![](https://i.imgur.com/kaV1hwm.png)
  - ![](https://i.imgur.com/W4m17j6.png)
- [Bias correction in data assimilation | Hans Hersbach and Dick Dee | 2014](https://www2.atmos.umd.edu/~dkleist/docs/da/ECMWF_DA_TUTORIAL/Hersbach_BiasCorrection.pdf)
  - **Most assimilation systems assume unbiased models and unbiased data**
  - {% gi 6 2-2-2 %}
    ![](https://i.imgur.com/bo9h7ek.png)
    ![](https://i.imgur.com/pDrburZ.png)
    ![](https://i.imgur.com/mjTDphh.png)
    ![](https://i.imgur.com/SHkJlVq.png)
    ![](https://i.imgur.com/p6f4C6M.png)
    ![](https://i.imgur.com/h0PecxM.png)
    {% endgi %}
- [Observational bias correction in data assimilation and an overview of satellite data monitoring | Niels Bormann | 2024](https://events.ecmwf.int/event/375/contributions/4263/attachments/2312/4041/Bormann_TC_2024_Bcor_SAT.pdf)
  - {% gi 6 2-2-2 %}
    ![](https://i.imgur.com/EgMLRiG.png)
    ![](https://i.imgur.com/FdXSSuB.png)
    ![](https://i.imgur.com/u30uZDp.png)
    ![](https://i.imgur.com/FUdxC7X.png)
    ![](https://i.imgur.com/aqdNCjg.png)
    ![](https://i.imgur.com/VGbcixN.png)
    {% endgi %}