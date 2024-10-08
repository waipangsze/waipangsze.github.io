---
layout: post
title: MPAS | use_mp_re/config_microp_re -- use effective radii computed in mp schemes in RRTMG
categories: [MPAS]
tags: [WRF,Parametrization]
author: wpsze
mathjax: true
mathjax_autoNumber: true
index_img: 
banner_img: 
---

# In WRF,

WRF Model Version 3.8: UPDATES

Add a new namelist, use_mp_re, to control the interaction between effective radii computed in some microphysics with RRTMG radiation.
{:.info}

use_mp_re                           = 1       ! whether to use effective radii computed in mp schemes in RRTMG
- 0: do not use; 
- 1: use effective radii
- (The mp schemes that compute effective radii are 3,4,6,7,8,10,14,16,18,24,26,28,50-53,55)

# In MPAS Version 7.0,

A fix for the communication of effective radii between the WSM6 and RRTMG schemes when config_microp_re=true.
{:.info}

```xml
<var name="re_cloud" type="real" dimensions="nVertLevels nCells Time" units="m"
        description="effective radius of cloud water droplets"
        packages="mp_thompson_in;mp_wsm6_in"/>

<var name="re_ice"  type="real" dimensions="nVertLevels nCells Time"  units="m"
        description="effective radius of cloud ice crystals"
        packages="mp_thompson_in;mp_wsm6_in"/>

<var name="re_snow" type="real" dimensions="nVertLevels nCells Time"  units="m"
        description="effective radius of snow crystals"
        packages="mp_thompson_in;mp_wsm6_in"/>

<var name="rre_cloud" type="real" dimensions="nVertLevels nCells Time" units="microns"
        description="effective radius of cloud water droplets calculated in RRTMG radiation"/>

<var name="rre_ice" type="real" dimensions="nVertLevels nCells Time" units="microns"
        description="effective radius of cloud ice crystals calculated in RRTMG radiation"/>

<var name="rre_snow" type="real" dimensions="nVertLevels nCells Time" units="microns"
        description="effective radius of snow crystals calculated in RRTMG radiation"/>
```

# References
1.[Comparison of WRF and Regional MPAS: Ensuring Consistent Physics Configurations, 2022](https://www2.mmm.ucar.edu/wrf/users/workshops/WS2022/full_presentations/session4.wong.pdf)

# Papers

1. (1994) Kiehl, J. T. [Sensitivity of a GCM climate simulation to differences in continental versus maritime cloud drop size. Journal of Geophysical Research: Atmospheres, 99(D11), 23107-23115.](https://agupubs.onlinelibrary.wiley.com/doi/abs/10.1029/94JD01117)

Extensive observations indicate a distinct difference between maritime and continental effective drop size, $r_e$, for warm clouds. The latest version of the NCAR Community Climate Model (CCM2) is used to explore the sensitivity of differentiating between continental and maritime $r_e$ on the simulated climate. **The results of this study indicate that the smaller drop size over continents leads to a reduction of surface-absorbed solar radiation from 20 to 60 $W m^{−2}$. This reduction in surface solar flux leads to a cooling of the continents by up to −3.5 K. The reduction in surface solar flux and temperature also leads to a reduction in latent heat flux and precipitation over land.** In the January simulation, there is a significant shift in tropical precipitation associated with the Australian monsoon. This shift leads to a response in the extra tropical geopotential height field over the western United States. All of these changes reduce biases in the current version of CCM2.
{:.info}

2. (1994) J.T. Kiehl. [Cloud Formation and Radiative Modeling in the NCAR CCM2: The Present and Future](https://ntrs.nasa.gov/api/citations/19960000201/downloads/19960000201.pdf), National Center for Atmospheric Research, U.S.A.

Although the CCM2 is generally an improvement over CCM1, there are stilt biases in CCM2. These are: (1) northern summer continents that are too warm, (2) excessive precipitation over land, and (3) a weak Northern Hemisphere winter stationary wave pattern in the Pacific Northwest. It is believed that to a large degree these biases are related to cloud properties.

**Observations indicate that over continents the warm cloud'-drop effective radius is approximately 5 $\mu m$, while over oceans the drop size is more like 10 $\mu m$**. A sensitivity study has been carried out (Kiehl, 1994) where the distinction is made between continental and maritime drop size. This simulation was compared to a control where the effective drop size was set to 10 $\mu m$ everywhere. **The smaller drop size over continents led to higher cloud albedos over land regions. This, in turn, led to less solar radiation absorbed at the surface, which led to a reduction of surface temperature (maximum reduction of around 5 K)**. Furthermore, the latent heat flux over land decreased, which led to a decrease in local precipitation. Thus, adopting a more realistic cloud-drop size for land and ocean led to an improved climate simulation. In January, there was an improvement in the Australian monsoon simulation, with a. displacement of the maximum precipitation in the northeast. This shift in tropical heating led to a shift in the Northern Hemisphere planetary wave structure, which again decreased the known bias in the control model.

CCM2 employs a prescribFa zonally symmetric in-cloud liquid-water path (Kiehl et al., 1994). Hack and Kiehl (1994) have recently replaced this with a local diagnostic for in-cloud liquid water. This new approach to in-cloud liquid water leads to further improvements in the summer land-surface temperature bias. This approach also vastly improves the shortwave cloud forcing over the North Atlantic storm track region.

The conclusion of these studies is that improvements in the diagnosis of cloud microphysical properties (drop size and liquid water content) leads to a significant improvement in the simulated climate of CCM2. Future research in the area will focus on implementation of a prognostic formulation for cloud water and ice concentration.

3. Kiehl, J. T., Hack, J. J., Bonan, G. B., Boville, B. A., Williamson, D. L., & Rasch, P. J. (1998). [The National Center for Atmospheric Research Community Climate Model: CCM3. Journal of Climate, 11(6), 1131-1149.](https://doi.org/10.1175/1520-0442(1998)011<1131:TNCFAR>2.0.CO;2)
   
