---
layout: post
title: MPAS | Noah-MP
categories: [MPAS]
tags: [MPAS, NWP, WRF, ERA5, GFS, Noah-MP]
author: wpsze
date: 2026-10-13 06:50:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://gmd.copernicus.org/articles/16/5131/2023/gmd-16-5131-2023-f01-web.png
banner_img: https://gmd.copernicus.org/articles/16/5131/2023/gmd-16-5131-2023-f01-web.png
---

# MPAS release

1. [MPAS Version 8.2.0](https://github.com/MPAS-Dev/MPAS-Model/releases#release-v8.2.0)
   1. New physics
      1. The **Noah-MP v5.0.1** land-surface model is now available by setting `config_lsm_scheme = 'sf_noahmp'` in the `&physics` namelist group.
      2. **New static files** that include the `soilcomp`, `soilcl1`, `soilcl2`, `soilcl3`, and `soilcl4` fields are required when activating Noah-MP in the model.
2. [MPAS Version 8.2.3](https://github.com/MPAS-Dev/MPAS-Model/releases#release-v8.2.3)
   1. Correction to the computation of the `soil temperature (TSLB)` in the Noah-MP land surface scheme through the addition of **initialization of the soil liquid water (SH2O)** in the `noahmp_init` subroutine in module `mpas_atmphys_lsm_noahmpinit.F` prior to calling `NoahmpInitMain`
   2. Correction of the loop for Noah-MP snow initialization, capping snow water equivalent maximum at 2000 mm.
   3. Correction to the calculation of the 2-meter diagnostics (T2M, TH2M, and Q2) when using the Noah-MP land surface scheme.
3. [MPAS Version 8.3.0](https://github.com/MPAS-Dev/MPAS-Model/releases#release-v8.3.0)
   1. Addition of support for **15" MODIS land use dataset**. The **15" MODIS land use dataset** may be selected by setting the existing namelist option `config_landuse_data` to `'MODIFIED_IGBP_MODIS_NOAH_15s'` in the `&data_sources` namelist group. Use of this dataset requires a separate static dataset download. 
   2. Addition of an option to **skip processing of Noah-MP-only static fields** in the init_atmosphere core. Setting the new `config_noahmp_static` namelist option to `false` in the `&data_sources` namelist group prevents the Noah-MP static fields from being processed when `config_static_interp = true` in the namelist.init_atmosphere file
4. [MPAS Version 8.4.0](https://github.com/MPAS-Dev/MPAS-Model/releases#release-v8.4.0)
   1. Correct an issue with high q2 values over urban cells in Noah-MP. 
5. [MPAS Version 8.4.1](https://github.com/MPAS-Dev/MPAS-Model/releases#release-v8.4.1)
   1. Fix a bug that occurs when the number of soil layers in Noah-MP is not the default (4 layers), resulting in the model not conserving water.

# Prepare

1. New static files that include the `soilcomp`, `soilcl1`, `soilcl2`, `soilcl3`, and `soilcl4` fields are required.
2. For high resolution mesh like 500m or below, you may apply supersampling as 3.
3. Be carefull that format of nc file like netcdf4 or pnetcdf
4. For atm step, 
   1. add `NoahmpTable.TBL`
   2. config_lsm_scheme = 'sf_noahmp'

# Running & Issues

1. [MPAS-A stops and raises error with Noah MP land surface model option on global variable resolution 15km-3km mesh | Feb 17, 2025](https://forum.mmm.ucar.edu/threads/mpas-a-stops-and-raises-error-with-noah-mp-land-surface-model-option-on-global-variable-resolution-15km-3km-mesh.20949/)
   1. The model stops and raises the following error: `CRITICAL ERROR: NaN detected in 'w' field.`
   2. We found the issue that caused MPAS crash.This is because, SWE from ERA5 is up to 10m, which leads to more than 30m of snow depth. As a result, NoahMP snow package becomes physically unreasonable and yields TSK that is totally wrong. TSK subsequently affects radiation, eventually leading to model crash.
   3. If you din't feel like to wait until the release of MPASV8.3.0, then you can modify the code:
2. [High Urban Moisture with NOAH-MP in MPAS-Atmosphere 8.2.3 | May 29, 2025 ](https://forum.mmm.ucar.edu/threads/high-urban-moisture-with-noah-mp-in-mpas-atmosphere-8-2-3.22160/)
   1. I noticed that urban areas quickly develop high q2, despite qflx being lower than surrounding areas.
3. [Snow depth error of using ERA5 driving MPAS | Apr 5, 2025](https://forum.mmm.ucar.edu/threads/snow-depth-error-of-using-era5-driving-mpas.21541/#post-52579#1)
   1. We have explored the reason for the MPAS crash. We believe that the information of SWE from ERA5 is not correct. A 10m SWE leads to more than 30m of snow depth, and the snow package in NoahMP cannot handle such a deep snow.
   2. We will fix this problem in MPASv8.3.0, which will be released before the end of June 2025. As a temporary fix, please modify the code

# Papers

1. Zhang, Z., He, C., Berner, J., Jaye, A., Barlage, M., Liu, C., et al. (2026). **Extending MPAS-NoahMP model system capability beyond weather timescales: Evaluation of hydroclimate and land-atmosphere interactions**. Journal of Geophysical Research: Atmospheres, 131, e2025JD045334. [https://doi.org/10.1029/2025JD045334](https://agupubs.onlinelibrary.wiley.com/doi/epdf/10.1029/2025JD045334)

# Links

1. [MPAS | MPAS UCM | Urban Canopy Model | HKUST](https://waipangsze.github.io/2026/08/18/MPAS-MPAS-UCM-Urban-Canopy-Model-HKUST/)