---
layout: post
title: MPAS | WRF | module_mp_thompson_cldfra3 | cloud fraction
categories: [MPAS]
tags: [NWP, MPAS, WRF, cloud fraction]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-06-03 08:04:00
index_img: 
banner_img: 
---

# Cloud fraction

Cloud fraction is an important variable in numerical weather prediction (NWP) models that help to improve the accuracy of weather forecasts. It is a measure of the fraction of the sky that is covered by clouds and is typically used to describe the amount of cloud cover at a given location and time. Accurate estimates of cloud fraction are essential for a wide range of applications in meteorology and climate science, including weather forecasting, climate modeling, and aviation safety.

- In NWP models, cloud fraction is typically calculated using output from the model's microphysics parameterization schemes. These schemes simulate the formation and evolution of cloud droplets and ice crystals and produce output variables that can be used to estimate cloud cover. The model output may include variables such as cloud water content, cloud ice content, and cloud optical thickness, which can be used to calculate cloud fraction using various algorithms and assumptions.
- In general, accurate estimates of cloud fraction are essential for improving the accuracy of weather forecasts and for understanding the complex interactions between clouds, radiation, and the atmosphere.

## Impact of cloud fraction on short wave radition

**Clouds have a significant impact on the amount of shortwave radiation that reaches the Earth's surface.** When there are no clouds, most of the incoming solar radiation reaches the surface, causing it to warm up. However, when there are clouds, they reflect some of the incoming solar radiation back into space, reducing the amount of shortwave radiation that reaches the Earth's surface. The amount of shortwave radiation that is reflected by clouds depends on the cloud fraction, as a higher cloud fraction means more incoming solar radiation is reflected back into space.

![https://visibleearth.nasa.gov/images/54219/cloud-effects-on-earths-radiation](https://i.imgur.com/bKcnSYR.png){width=400}

# The formulation cloud fraction options in WRF and MPAS

## WRF

```namelist
icloud = 1,	! cloud effect to the optical depth in radiation
            (only works for ra_sw_physics = 1,4 and ra_lw_physics = 1,4)
            Since 3.6, this also controls the cloud fraction options
            1 = with cloud effect, and use cloud fraction option 1
                (Xu-Randall method) 
            0 = without cloud effect
            2 = with cloud effect, and use cloud fraction option 2 (0/1 based
                on threshold
            3 = with cloud effect, and use cloud fraction option 3, based on
                Sundqvist et al. (1989) (since 3.7)
```

- **icloud=1** 
  - [Xu, Kuan-Man, and Randall, D A. 1996. "**A semiempirical cloudiness parameterization for use in climate models**". United States. https://doi.org/10.1175/1520-0469(1996)053<3084:ASCPFU>2.0.CO;2.](https://doi.org/10.1175/1520-0469(1996)053)
- **icloud=3**
  - [Sundqvist, Hilding, Erik Berge, and Jón Egill Kristjánsson. "**Condensation and Cloud Parameterization Studies with a Mesoscale Numerical Weather Prediction Model**", Monthly Weather Review 117, 8 (1989): 1641-1657, doi: https://doi.org/10.1175/1520-0493(1989)117<1641:CACPSW>2.0.CO;2](https://doi.org/10.1175/1520-0493(1989)117)

{% gi 5 3-2 %}
![](https://i.imgur.com/s0YY0UF.png)
![](https://i.imgur.com/yfZReoj.png)
{% endgi %}

## MPAS

- **core_atmosphere/Registry.xml**
```xml
<nml_option name="config_radt_cld_scheme" type="character" default_value="suite" in_defaults="false"
        units="-"
        description="configuration for calculation of horizontal cloud fraction"
        possible_values="`suite',`cld_fraction',`cld_incidence'"/>
```

![src/core_atmosphere/physics/mpas_atmphys_control.F](https://i.imgur.com/jHwvS5d.png){width=400}

- core_atmosphere/physics/**mpas_atmphys_driver_cloudiness.F**

```fortran
! * since we removed the local variable radt_cld_scheme from mpas_atmphys_vars.F, now defines radt_cld_scheme
!   as a pointer to config_radt_cld_scheme.
!   Laura D. Fowler (laura@ucar.edu) / 2017-02-16.
 call mpas_pool_get_config(configs,'config_radt_cld_scheme',radt_cld_scheme)
```

![MPAS-Model/src/core_atmosphere/physics/mpas_atmphys_driver_cloudiness.F](https://i.imgur.com/rPDQZGx.png){width=400}

{% note primary %}
- **call `calc_cldfraction`**
- **call `cal_cldfra3`**
{% endnote %}

# WRF & MPAS-A Support Forum

- [Cloud fraction limit to 0.9 in **module_mp_thompson_cldfra3** | Apr 23, 2024](https://forum.mmm.ucar.edu/threads/cloud-fraction-limit-to-0-9-in-module_mp_thompson_cldfra3.16847/)
  - The routine `cal_cldfrac3` limits the **maximum value of cloud fraction to 0.9 (see line 134)**. Is there a plausible reason for that, or is this just a mistype?
  - **Ming Chen:** Please see whether you can find information in the paper: [hompson, Gregory, Paul R. Field, Roy M. Rasmussen, William D. Hall, 2008: Explicit Forecasts of Winter Precipitation Using an Improved Bulk Microphysics Scheme. Part II: Implementation of a New Snow Parameterization. Mon. Wea. Rev., 136, 5095–5115. doi:10.1175/2008MWR2387.1](https://journals.ametsoc.org/doi/10.1175/2008MWR2387.1)
  - **Ming Chen:** I explore this issue a bit more. What I found is that, 
    - **based on some NCEP-EMC tests, the icloud3 option made far too many clouds when compared to observations.** 
    - I guess this could be the **reason** that `cal_cldfrac3` limits the maximum value of cloud fraction to 0.9. 
    - However, I didn't find any publications that specifically address this issue. 
    - I believe **you can tune the model behavior by modifying the maximum value of cloud fraction** at line 134. Hope this is helpful for you.
  - I also found in **my experiments that this formulation overestimates the cloud fraction**, which results in poor energy balance on the surface in the Amazon basin.
  - When `cal_cldfrac3` limits the maximum value of cloud fraction from 0.9 to a Smaller values, WRF will overestimates the cloud fraction to a lesser extent ？
  - **Ming Chen:** This is what is expected. However, I have to say that it may be case-dependent. **if your case still shows large overestimation, you may need to further reduce the maximum cldfra value.**
- [cldfrac_tot_UPP is zero at the first time step | Dec 26, 2024](https://forum.mmm.ucar.edu/threads/cldfrac_tot_upp-is-zero-at-the-first-time-step.20392/#post-49704)
  - Yes this is an expected behavior. **`cldfrac_tot_UPP` is a diagnostic variable that is calculated based on `cldfrc`**. 
  - **At the initial time, both are set to zero**.
  - **MPAS doesn't calculate cldfra in the first time step** because it is based on other physical variables like **qc,qi, rh etc**. **Some of these variables are not available at the beginning of model run**. For this reason, I don't think it is practical to diagnose cldfra at the initial time.
- [CLDFRA vs QCLOUD variables | Jan 6, 2024](https://forum.mmm.ucar.edu/threads/cldfra-vs-qcloud-variables.15170/#post-38214)
  - **`CLDFRA` is calculated in radiation driver**. Therefore, **it is updated at the time of radiation calculation**, which is determined by the namelist option radt. If you want to have this variable at every time step, you will need to set radt equal to time step. However, we won't recommend doing so because radiation schemes are computationally expensive.
