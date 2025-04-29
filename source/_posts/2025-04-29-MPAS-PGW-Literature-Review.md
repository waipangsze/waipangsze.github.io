---
layout: post
title: MPAS | PGW Literature Review 
categories: [MPAS]
tags: [WRF, MPAS, HPC, NWP, PGW]
author: wpsze
date: 2025-04-29 11:26:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/3h7OC3S.png
banner_img: https://i.imgur.com/3h7OC3S.png
---

- [**Pseudo-Global Warming (PGW) - Future Projection**](https://waipangsze.github.io/2024/09/30/PGW/)
- [**NWP | Pseudo-Global-Warming (PGW) hands-on | ERA5 | GFS**](https://waipangsze.github.io/2025/02/03/NWP-PGW-hands-on-ERA5-GFS/)
- [**NWP | PGW | To prepare the ERA5 GRIB file**](https://waipangsze.github.io/2025/02/05/NWP-PGW-To-prepare-the-ERA5-GRIB-file/)

---

# PGW Literature Review on MPAS-A

- [Nunez Ocasio, Kelly M., and Erin M. Dougherty. "The effect of **pseudo‐global warming** on the weather‐climate system of Africa in a convection‐permitting model." Geophysical Research Letters 51.24 (2024): e2024GL112341.](https://agupubs.onlinelibrary.wiley.com/doi/pdfdirect/10.1029/2024GL112341)
  - This study uses the **pseudo‐global warming (PGW) method** in convection‐permitting simulations to assess the impact of anthropogenic warming on Africa's weather‐climate system prior to and during Hurricane Helene's (2006) formation.
  - **To our knowledge, no study have used the PGW method to examine tropical precipitation with the MPAS‐A model.**
  - **MPAS‐A version 8.0.1 with the Limited‐Area configuration**
  - **A 15‐km–3‐km variable‐resolution** mesh was used with the an elliptically shaped 3‐km highresolution refinement region of the mesh.
    - **At 3 km, deep convection was explicitly resolved**.
  - ![](https://i.imgur.com/3h7OC3S.png)
  - To simulate the period of 1200 UTC 8 September–1800 UTC 13 September 2006
    - In addition to initializing the model at **1200 UTC on 8 September**, two other sets of experiments were completed: initializing the model at **00 UTC on 5 September** and initializing at **00 UTC on 6 September**.
    - **A limitation of the study** is that it uses *only one future projection scenario* and one climate model for simulations of short integration time.
  - **The dataset (Download)** 
    - [MPAS-A pseudo-global warming (PGW) experiment with convection-permitting resolution and regional configuration using the Model for Prediction Across Scales (MPAS) version 8.0.1](https://gdex.ucar.edu/dataset/448.html)
      - ![](https://i.imgur.com/KiMlEds.png){width=300}
- [Kelly Marie Núñez Ocasio, Erin Dougherty, Zachary Moon, et al. Response of African Easterly Waves to a **Warming Climate**: A Convection-Permitting Approach. ESS Open Archive . April 24, 2025.](https://essopenarchive.org/doi/full/10.22541/essoar.174547926.68665522/v1)
  - The **MPAS-A version 8.0.1 (Skamarock et al., 2012) regional convection-permitting data** used here, as described by Nunez Ocasio and Dougherty (2024a).
  - The **buffer region between the 15-km resolution mesh and the refined 3-km resolution area ensures smooth transitions** from coarse to fine resolution while maintaining computational efficiency and effectively reducing any artificial features from the nested domain-type boundaries as in other models like WRF.

# African easterly wave (AEW) tropical cyclogenesis (TCG) 

- [Núñez Ocasio, K. M., & Rios‐Berrios, R. (2023). African easterly wave evolution and tropical cyclogenesis in a pre‐Helene (2006) Hindcast using the Model for Prediction Across Scales‐Atmosphere (MPAS‐A). Journal of Advances in Modeling Earth Systems, 15(2), e2022MS003181.](https://agupubs.onlinelibrary.wiley.com/doi/pdfdirect/10.1029/2022MS003181)
  - Tropical cyclogenesis (TCG) remains an elusive phenomenon partly due to the limited understanding of complex water vapor-convection-wave interactions. The Model for Prediction Across Scales-Atmosphere (MPAS-A) was used to study the TCG of the African easterly wave (AEW) that became Hurricane Helene (2006). The two main objectives were: 
    - (a) evaluate the capability of MPAS-A to simulate TCG from an AEW by comparing MPAS-A—initialized with the Integrated Forecasting System (IFS) and the Global Forecast System (GFS)—with observations together with reanalysis and, 
    - (b) use the hindcast to investigate the role of moisture in the mechanisms that led to Helene's TCG.
  - **MPAS-A version 7.1**
  - A **uniform 15-km** cell-spacing global grid and 55 vertical levels
  - The model outputs were interpolated to a latitude and longitude **grid equivalent of a 0.25° × 0.25°** horizontal resolution to be comparable to ERA5
  - Data Availability Statement:
    - Post-processed model outputs and the namelists for the MPAS-A simulations can be accessed at <https://doi.org/10.5065/t224-6s94>. IMERG data were provided by NASA's Goddard Earth Sciences Data and Information Services Center (<https://disc.gsfc.nasa.gov/>). The ERA5 (<https://rda.ucar.edu/datasets/ds633.0/>) and GFS (<https://rda.ucar.edu/datasets/ds083.2/>) data were accessed via NCAR Research Data Archive through the Computational and Information Systems Laboratory (CISL). The HURDAT2 track was accessed through the NOAA NHC official page (<https://www.nhc.noaa.gov/data/hurdat/hurdat2-format-atlantic.pdf>).
- **Dr. Kelly Nuñez Ocasio**
  - National Center for Atmospheric Research
  - Title: **The Multiscale Nature and Moisture Dependency of Tropical Cyclone Formation** (2023-02-09)

<iframe width="560" height="315" src="https://www.youtube.com/embed/JQwKkEmRTPg" title="UW-AOS Colloquium - 2/20/2023 - Kelly Núñez Ocasio, NCAR" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>