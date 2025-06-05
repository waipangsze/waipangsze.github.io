---
layout: post
title: NWP | Why is RH preferred over mixing ratio for initial moisture in NWP models
categories: [MPAS]
tags: [MPAS, NWP, WRF, ERA5, GFS, RH, mixing ratio]
author: wpsze
date: 2025-06-05 10:18:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/H8Kr0RO.png
banner_img: https://i.imgur.com/H8Kr0RO.png
---

# MPAS-A

 
- <https://mpas-dev.github.io/atmosphere/mpas-a_release_notes.html>
- <https://github.com/MPAS-Dev/MPAS-Model/commit/635532ebd0677675eafcaa07c1bc701997b896fe>

MPAS-Atmosphere Version 5.3\
22 March 2018\

- This minor release of MPAS corrects an issue with the **initialization of the water vapor mixing ratio field in MPAS-Atmosphere**, and it also adds two other fixes that should not affect model results.
- The MPAS-Atmosphere initialization program (init_atmosphere_model) had previously assumed that any relative humidity field provided in an intermediate file was specified with respect to liquid water at or above 273.15 K, and with respect to ice below this temperature. However, **intermediate files produced by the WPS's ungrib.exe program have the RH field adjusted so that is with respect to liquid water everywhere. With this release, the init_atmosphere core correctly interprets the RH field in producing the initial water vapor mixing ratio field.** 
- Note that, when initializing from specific humidity (SPECHUMD) rather than RH, there is no change to results.

![](https://i.imgur.com/H8Kr0RO.png){width=400}
 
- <https://github.com/MPAS-Dev/MPAS-Model/commit/635532ebd0677675eafcaa07c1bc701997b896fe>

```text
Merge branch 'atmosphere/fix_RH_wrt_liquid_water' into hotfix-v5.3 · MPAS-Dev/MPAS-Model@635532e
This merge corrects an error in deriving the water vapor mixing ratio field (QV)
from relative humidity (RH) for real-data cases.
Under the assumption that real-data intermediate files were produc...
``` 

# The Role of Relative Humidity in Moisture Initialization for Numerical Weather Prediction Models  

Numerical weather prediction (NWP) models rely on accurate initialization of atmospheric variables to produce reliable forecasts. Among these variables, the representation of moisture specifically the water vapor mixing ratio is critical for simulating cloud formation, precipitation, and latent heat release. While mixing ratio directly quantifies the mass of water vapor per unit mass of dry air, **NWP systems often initialize moisture fields using relative humidity (RH) rather than directly ingesting mixing ratio values** from global models like the Global Forecast System (GFS) or the Integrated Forecasting System (IFS). This preference stems from **RH’s thermodynamic consistency, its ability to standardize moisture representation across diverse data sources, and its role in mitigating phase-related ambiguities in saturation calculations**.  

---

## Thermodynamic Consistency and Saturation Adjustments  

### Phase-Agnostic Moisture Representation  

Relative humidity is defined as the ratio of the current water vapor pressure to the saturation vapor pressure at a given temperature. This normalization ensures that RH accounts for temperature-dependent saturation limits, which vary significantly between liquid water and ice at subfreezing temperatures[^1][^5]. When initializing moisture fields, NWP models such as the Weather Research and Forecasting (WRF) system **convert RH to mixing ratio using internally consistent saturation vapor pressure formulas**. 

- **This step is necessary because source datasets (e.g., GFS) may define saturation with respect to ice at higher atmospheric levels, while the model’s `microphysics schemes` often assume saturation over liquid water**[^4][^6]. 

By recalculating mixing ratio from RH, the model enforces uniformity in phase assumptions, avoiding discontinuities in moisture fields that could arise from conflicting definitions of saturation states[^1].  

For example, the WRF Preprocessing System (WPS) adjusts RH values in intermediate files to align with liquid-water saturation across all vertical levels. This adjustment ensures that the model’s initialization routine such as the `init_atmosphere_model` core interprets moisture fields correctly, even when source data implicitly uses ice-phase saturation at colder temperatures[^4]. Directly using mixing ratio from external sources would bypass this critical normalization, potentially introducing biases in cloud initialization and latent heat calculations[^5].  

---

## Mitigating Source Data Heterogeneity  

### Standardization Across Input Datasets  

Global models and reanalysis products **employ diverse parameterizations for moisture variables**. For instance, the GFS outputs specific humidity (SPECHUMD), while satellite retrievals and regional models might provide RH or mixing ratio with varying definitions (e.g., over moist vs. dry air)[^2][^6]. 

- Converting these variables to RH first allows the NWP system to **harmonize moisture inputs using a dimensionless, universally comparable metric**. Subsequent conversion to mixing ratio using the model’s own saturation formulas ensures that all moisture fields adhere to **the same thermodynamic assumptions, regardless of source**[^6].  

This approach is particularly vital when integrating satellite data, such as GOES sounder observations, which provide RH profiles for cloud initialization[^1]. By leveraging RH’s bounded nature (0–100%), models can more easily assimilate these observations without introducing unphysical extremes (e.g., negative mixing ratios)[^4][^5]. For example, grid analysis nudging in WRF often adjusts RH rather than mixing ratio to avoid destabilizing the model with unrealistic moisture increments[^5].  

---

## Handling Phase Transitions and Cloud Microphysics  

### Dynamic Adjustment to Local Conditions  

Mixing ratio alone does not encode information about the atmosphere’s proximity to saturation, which is essential for triggering condensation, evaporation, or phase changes in clouds. 

- **By initializing with RH, the model dynamically computes mixing ratio based on local temperature and pressure profiles, ensuring that moisture fields remain thermodynamically consistent with the evolving atmospheric state**[^1][^5]. This is especially critical in upper atmospheric layers, where small temperature fluctuations can drastically alter saturation vapor pressure.  

A case study in WRF demonstrated that adjusting RH profiles by 12% reduced root-mean-square error (RMSE) in shortwave irradiance forecasts by 57.8%, highlighting RH’s sensitivity to microphysical processes[^5]. Directly prescribing mixing ratio would decouple moisture content from temperature-dependent saturation limits, compromising the model’s ability to simulate cloud formation and dissipation accurately[^1].  

---

## Practical Considerations in Data Assimilation  

### Boundedness and Numerical Stability 

RH’s $0–100\%$ range provides inherent safeguards against unphysical moisture values during data assimilation. When external observations or model outputs contain noise (e.g., slightly negative RH due to interpolation artifacts), the NWP system can apply quality control thresholds without risking large errors in absolute moisture content[^4]. In contrast, directly assimilating mixing ratio could propagate outliers—such as negative values—into the model, destabilizing precipitation and boundary layer schemes[^4][^5].  

For example, a user comparing GFS analysis RH to WPS-processed `met_em` files observed minor RH excursions beyond 100% or below 0% in the latter, likely due to interpolation or unit conversion artifacts[^4]. These excursions were minimized by recalculating mixing ratio from RH using the model’s internal physics, underscoring the practicality of RH-based initialization[^4][^6].  

## When Initializing from Specific Humidity (SPECHUMD)?

When the initialization uses **specific humidity (which is closely related to mixing ratio)**, there is no need for conversion or adjustment, so results remain unchanged. This indicates that the use of RH is primarily to convert from a normalized moisture variable into an absolute moisture content consistent with the model's physics, rather than to replace direct moisture variables if they are already available (<https://github.com/NCAR/WPS/blob/master/ungrib/src/rrpr.F>).

---

## Conclusion  

**The preference for RH in moisture initialization arises from its thermodynamic consistent, adaptability to diverse data sources, and role in maintaining numerical stability**. By converting RH to mixing ratio within the NWP framework, models enforce consistent phase assumptions, mitigate source data discrepancies, and dynamically align moisture fields with local atmospheric conditions. While specific humidity (SPECHUMD) can be/not be used directly when available, RH remains indispensable for integrating heterogeneous observations and ensuring physically coherent moisture initialization. Future advancements may focus on enhancing RH assimilation techniques, particularly in reconciling ice-phase saturation definitions at high altitudes, to further improve cloud and precipitation forecasts.  

--- 

# References

[^1]: <https://journals.ametsoc.org/view/journals/mwre/128/11/1520-0493_2001_129_3911_nciugs_2.0.co_2.xml>
[^2]: <https://ntrs.nasa.gov/api/citations/20190002391/downloads/20190002391.pdf>
[^3]: <http://cola.gmu.edu/dirmeyer/COLA_KIAPS_Final_Report_28Feb2013.pdf>
[^4]: <https://forum.mmm.ucar.edu/threads/inconsistency-between-gfs-analysis-rh-and-met_em-rh.14255/>
[^5]: <https://journals.ametsoc.org/view/journals/wefo/36/2/WAF-D-20-0117.1.xml>
[^6]: <https://nwp-saf.eumetsat.int/site/download/documentation/rtm/docs_rttov12/rttov_gas_cloud_aerosol_units.pdf>
[^7]: <https://www.vaisala.com/en/blog/2018-10/relative-humidity-what-it-and-why-it-important>
[^8]: <https://www.weather.gov/media/epz/wxcalc/mixingRatio.pdf>
[^9]: <https://journals.ametsoc.org/downloadpdf/journals/wefo/aop/WAF-D-20-0117.1/WAF-D-20-0117.1.xml>
[^10]: <https://nhess.copernicus.org/articles/25/429/2025/>
[^11]: <https://bin.ssec.wisc.edu/pub/jordang/talkpost/MagicPWNWP_18Oct11.pdf>