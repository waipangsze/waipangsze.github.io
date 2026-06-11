---
layout: post
title: MPAS | Eddy dissipation rate (EDR) 
categories: [MPAS]
tags: [MPAS, NWP, HKO, TKE, EDR, CIT, LES]
author: wpsze
date: 2025-11-10 06:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/VsfV0li.png
banner_img: https://i.imgur.com/VsfV0li.png
---

- [**NWP | Eddy Dissipation Rate (EDR)**](https://waipangsze.github.io/2025/04/08/Eddy-Dissipation-Rate-EDR/)

---

# Analysis and Simulations for the Severe Turbulence Event Aloft Myanmar on 21 May 2024.

- **Leung, Christy Yan Yu**, et al. "Analysis and Simulations for the Severe Turbulence Event Aloft Myanmar on 21 May 2024." Meteorological Applications 32.3 (2025): e70058.
  - **HKO**
  - <https://rmets.onlinelibrary.wiley.com/doi/epdf/10.1002/met.70058>
  - MPAS-A
    - 60-3km
      - ![](https://i.imgur.com/PNzWRbU.png){width=400}
    - ![](https://i.imgur.com/tLlRegu.png)
    - ![](https://i.imgur.com/VsfV0li.png)
  - Deep Learning Model for Detection of Turbulence From AGW Features
  - Seamless Blended Significant Convection Forecast

# Tropical Aviation Turbulence Induced by the Interaction Between a Jet Stream and Deep Convection

- Chen, H., Leung, C. Y. Y., Cheung, P., Liu, H., Chan, S. T., & Shi, X. (2025). Predicting Convectively Induced Turbulence With Regionally Convection-Permitting Simulations. Asia-Pacific Journal of Atmospheric Sciences, 61(2), 16.
  - MPAS-A v7
    - We developed a method to estimate the eddy dissipation rate (EDR) using the **resolved wind field of the MPAS simulations**. (3D field)
    - ![](https://i.imgur.com/STlCvLZ.png)
    - ![](https://i.imgur.com/yftRfGo.png)
    - The **Graphical Turbulence Guidance (GTG) system**is indeed a multi-diagnostic algorithm developed by the National Center for Atmospheric Research (NCAR). It is primarily run on data from the National Centers for Environmental Prediction (NCEP), which is a division of the National Weather Service (NWS).
      - Within global aviation, the NWS operates the Washingt on World Area Forecast Center (WAFC), which is a vital part of the World Area Forecast System (WAFS).
      - <https://www.icao.int/sites/default/files/WACAF/MeetingDocs/2025/New%20SADIS%20API/2-WAFS-Gridded-Data.pdf>
        - ICAO Annex 3 - Meteorological Service for International Air Navigation, Appendix 4has the following information:
          - Turbulence shall be considered:
            - a) Severe when the peak value of EDR equals or exceeds 0.45;
            - b) moderate when the peak value is equal to or above 0.20 and below 0.45;
            - c) Light when the peak value is above 0.10 and below 0.20; and
            - d) nil when the peak value is below or equal to 0.10.
    - At higher altitudes, although the area with a minimum Richardson number of less than 0.25 is relatively small, it still triggers severe turbulence due to wave breakings.
    - Spatial distributions of the EDR calculated with different methods **at 10 km altitude,**
    - {% gi 2 2 %}
      ![](https://i.imgur.com/9Daxi8O.png)
      ![](https://i.imgur.com/ZNu828r.png)
      {% endgi %}

# References

- [Shi, X., Hagen, H. L., Chow, F. K., Bryan, G. H., & Street, R. L. (2018). Large-eddy simulation of the stratocumulus-capped boundary layer with explicit filtering and reconstruction turbulence modeling. Journal of the Atmospheric Sciences, 75(2), 611-637.](https://journals.ametsoc.org/view/journals/atsc/75/2/jas-d-17-0162.1.pdf)
  - ![](https://i.imgur.com/ZIGUYrp.png)
