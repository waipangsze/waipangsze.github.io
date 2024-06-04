---
layout: article
title: use_mp_re/config_microp_re -- use effective radii computed in mp schemes in RRTMG
categories: [MPAS]
tags: [WRF,Parametrization]
author: wpsze
mathjax: true
mathjax_autoNumber: true
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

