---
layout: post
title: MPAS | MPAS Version 8
categories: [MPAS]
tags: [MPAS, NWP, WRF, ERA5, GFS]
author: wpsze
date: 2025-02-26 10:50:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

# Timeline

- MPAS-Atmosphere Version 8.2.2 | 20 September 2024
- MPAS-Atmosphere Version 8.2.1 | 7 August 2024
- MPAS-Atmosphere Version 8.2.0 | 27 June 2024
- MPAS-Atmosphere Version 8.1.0 | 18 April 2024
- MPAS-Atmosphere Version 8.0.2 | 26 March 2024
- MPAS-Atmosphere Version 8.0.1 | 6 July 2023
- MPAS-Atmosphere Version 8.0.0 | 16 June 2023

# Links

1. [https://mpas-dev.github.io/](https://mpas-dev.github.io/)
2. [/MPAS-Model/releases](https://github.com/MPAS-Dev/MPAS-Model/releases)
3. [MPAS-Atmosphere Users' Guide V5.3](https://www2.mmm.ucar.edu/projects/mpas/mpas_atmosphere_users_guide_5.3.pdf)
4. [MPAS-Atmosphere Users' Guide V6.3](https://www2.mmm.ucar.edu/projects/mpas/mpas_atmosphere_users_guide_6.3.pdf)
5. [MPAS-Atmosphere Users' Guide V7.0](https://www2.mmm.ucar.edu/projects/mpas/mpas_atmosphere_users_guide_7.0.pdf)
6. [MPAS-Atmosphere Users' Guide V8.2.0](https://www2.mmm.ucar.edu/projects/mpas/mpas_atmosphere_users_guide_8.2.0.pdf)
7. [Joint WRF/MPAS Users Workshop 2024](https://www.mmm.ucar.edu/events/133129/agenda)

# MPAS-Atmosphere Version 8.0.0

- Changes to the initialization include:
  - **Enable parallel remapping of static fields** with arbitrary graph partition files; special CVT partition files are no longer required.  
    - Run init atmosphere model with **only one MPI task** specified to create static.nc
    - Note that it is critical for this step that the initialization core is **run serially**;
  - Reset the default for the lower air-temperature extrapolation **(config_extrap_airtemp) from 'linear' to 'lapse-rate' in the namelist**. This applies to initialization and to lateral boundary condition generation for MPAS-A.
  - Set the condition for the lower extrapolation of the horizontal velocity such that it **returns the lowest analysis level value** instead of a linear extrapolation when the requested level is below the analysis level.
  - Create a new init case (13) for creating 3-d CAM-MPAS grids.
- Changes to the physics include:
  - Update the **Noah land surface scheme to the WRF 4.5** release.
  - Update the **MM5 surface layer scheme to the WRF 4.5** release.
  - Implemented the **CCPP-compliant version** of:
    - the revised MM5 surface layer scheme;
    - the parameterization of the gravity-wave drag over orography;
    - the YSU Planetary Boundary Layer scheme;
    - the scale-aware nTiedtke parameterization of convection; and
    - the WSM6 cloud microphysics parameterization.
  - Correct the initialization of the **maximum snow albedo over sea ice points (now set to 0.75** instead of 0).
  - Fix the option that defines the surface albedo over sea ice points. The default option is now set to zero and the default value for **the surface albedo over sea ice points is set to 0.65**.
  - In the dynamics, rework the computation of the advective tendency of potential temperature needed as forcing in the **nTiedtke** and **Grell-Freitas** parameterizations of deep convection. The advective tendency of potential temperature is **now computed the same way** for the nTiedtke and Grell-Freitas convection schemes.

{% note primary %}
IMPORTANT NOTE: The updated physics schemes require new look-up tables, in particular, for the Noah land-surface model. The **checkout_data_files.sh** script that is run at build time should correctly update these tables, but tables in other run directories will need to be manually updated.
{% endnote %}

- Include changes to the initialization and to MPAS-A such that this release can be **directly used in CESM/CAM**.